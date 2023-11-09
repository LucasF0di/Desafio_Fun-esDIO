def mostrar_opcoes():
    escolhas = """\n
    ================ OPÇÕES ================
    [1] Realizar depósito
    [2] Efetuar retirada
    [3] Exibir Histórico de Transações
    [4] Abrir Nova Conta
    [5] Listar Contas
    [6] Cadastrar Novo Cliente
    [0] Encerrar Programa
    => """
    return input(escolhas)

def efetuar_deposito(saldo_atual, valor, historico, /):
    if valor > 0:
        saldo_atual += valor
        historico += f"Depósito: R$ {valor:.2f}\n"
        print("\n=== Depósito realizado com sucesso! ===")
    else:
        print("\n Operação falhou! Valor inválido. Tente novamente.")

    return saldo_atual, historico

def realizar_saque(*, saldo_atual, valor, historico, limite, num_saques, limite_saques):
    excedeu_saldo = valor > saldo_atual
    excedeu_limite = valor > limite
    excedeu_saques = num_saques >= limite_saques

    if excedeu_saldo:
        print("\n@@@ Operação falhou! Saldo insuficiente. Verifique seu saldo e tente novamente. @@@")

    elif excedeu_limite:
        print("\n@@@ Operação falhou! Valor excede o limite permitido. Tente um valor menor. @@@")

    elif excedeu_saques:
        print("\n@@@ Operação falhou! Máximo de saques permitidos atingido. Aguarde um pouco e tente novamente. @@@")

    elif valor > 0:
        saldo_atual -= valor
        historico += f"Retirada: R$ {valor:.2f}\n"
        num_saques += 1
        print("\n=== Saque concluído com sucesso! ===")

    else:
        print("\n@@@ Operação falhou! Valor inválido. Tente novamente. @@@")

    return saldo_atual, historico

def exibir_historico(saldo_atual, /, *, historico):
    print("\n================ HISTÓRICO ================")
    print("Nenhuma transação realizada." if not historico else historico)
    print(f"\nSaldo atual: R$ {saldo_atual:.2f}")
    print("==========================================")

def criar_novo_cliente(clientes_cadastrados):
    cpf = input("Informe o CPF (apenas números): ")
    cliente_existente = filtrar_cliente(cpf, clientes_cadastrados)

    if cliente_existente:
        print("\n@@@ CPF já cadastrado! Por favor, verifique os dados ou entre em contato conosco. @@@")
        return

    nome = input("Informe o nome completo: ")
    data_nascimento = input("Informe a data de nascimento (dd-mm-aaaa): ")
    endereco = input("Informe o endereço (logradouro, nro - bairro - cidade/sigla estado): ")

    clientes_cadastrados.append({"nome": nome, "data_nascimento": data_nascimento, "cpf": cpf, "endereco": endereco})

    print("\n=== Cliente cadastrado com sucesso! ===")


def filtrar_cliente(cpf, clientes_cadastrados):
    clientes_filtrados = [cliente for cliente in clientes_cadastrados if cliente["cpf"] == cpf]
    return clientes_filtrados[0] if clientes_filtrados else None


def criar_conta_bancaria(agencia, num_conta, clientes_cadastrados):
    cpf = input("Informe o CPF do cliente: ")
    cliente = filtrar_cliente(cpf, clientes_cadastrados)

    if cliente:
        print("\n=== Conta bancária criada com sucesso! ===")
        return {"agencia": agencia, "num_conta": num_conta, "cliente": cliente}

    print("\n@@@ Cliente não encontrado. Por favor, verifique os dados ou entre em contato conosco. @@@")
    return None


def listar_contas_bancarias(contas_bancarias):
    if not contas_bancarias:
        print("\n=== Nenhuma conta bancária cadastrada no momento. ===")
        return

    print("\n=============== LISTA DE CONTAS BANCÁRIAS ================")
    for conta_bancaria in contas_bancarias:
        print("\nAgência:\t", conta_bancaria['agencia'])
        print("C/C:\t\t", conta_bancaria['num_conta'])
        print("Titular:\t", conta_bancaria['cliente']['nome'])
        print("=" * 60)


def iniciar_programa():
    LIMITE_SAQUES = 3
    AGENCIA_PADRAO = "0001"

    saldo_atual = 0
    limite_saque = 500
    historico_transacoes = ""
    num_saques_efetuados = 0
    clientes_cadastrados = []
    contas_bancarias = []

    while True:
        opcao = mostrar_opcoes()

        if opcao == "1":
            valor_deposito = float(input("Informe o valor do depósito: "))

            saldo_atual, historico_transacoes = efetuar_deposito(saldo_atual, valor_deposito, historico_transacoes)

        elif opcao == "2":
            valor_saque = float(input("Informe o valor do saque: "))

            saldo_atual, historico_transacoes = realizar_saque(
                saldo_atual=saldo_atual,
                valor=valor_saque,
                historico=historico_transacoes,
                limite=limite_saque,
                num_saques=num_saques_efetuados,
                limite_saques=LIMITE_SAQUES,
            )

        elif opcao == "3":
            exibir_historico(saldo_atual, historico=historico_transacoes)

        elif opcao == "6":
            criar_novo_cliente(clientes_cadastrados)

        elif opcao == "4":
            num_conta_bancaria = len(contas_bancarias) + 1
            conta_bancaria = criar_conta_bancaria(AGENCIA_PADRAO, num_conta_bancaria, clientes_cadastrados)

            if conta_bancaria:
                contas_bancarias.append(conta_bancaria)

        elif opcao == "5":
            listar_contas_bancarias(contas_bancarias)

        elif opcao == "0":
            print("\n=== Programa encerrado. Até logo! ===")
            break

        else:
            print("\n@@@ Operação inválida! Escolha uma opção válida. @@@")
            
iniciar_programa()
