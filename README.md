# Sistema_Bancario2.0
Otimizando sistema bancario
# Definições da conta
saldo = 0
limite = 500
extrato_lista = []  # Alteração: extrato deve ser uma lista para armazenar transações
usuarios = [] # Lista de usuarios
contas = [] # lista de contas
numero_saques = 1
LIMITE_SAQUES = 3
numero_conta = 1
AGENCIA = '0001'

def menu():
    print('⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯MENU⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯\n'
    '[D]Depositar\n'
    '[S]Sacar\n'
    '[E]Extrato\n'
    '[NU]Criar usuário\n'
    '[NC]Criar conta\n'
    '[LC]Listar contas\n'
    '[Q]Sair')
    print('⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯')


def opcao_sistema():
    while True:

        print('⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯')
        opcao = input('Escolha uma opção ➡  ').upper().strip()
        print('⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯')

        if opcao == 'D':
            depositar()

        elif opcao == 'S':
            sacar()

        elif opcao == 'E':
            extrato()

        elif opcao == 'NU':
            criar_usuario(usuarios)

        elif opcao == 'NC':
            numero_conta = len(contas) + 1
            conta = criar_conta(numero_conta, usuarios)

            if conta:
                contas.append(conta)
                numero_conta += 1

        elif opcao == 'LC':
            listar_contas(contas)

        elif opcao == 'Q':
            sair()
            break

        else:
            print('Opção inválida. Tente novamente!')


def criar_usuario(usuarios):

    global contas
    cpf = input('Informe o CPF (somente números): ')
    
    # validação do CPF
    while not cpf.isdigit() or len(cpf) != 11:
        print('CPF inválido. Por favor, informe um CPF válido.')
        cpf = input('Informe o CPF (somente números): ')

    cpf = int(cpf)
    usuario = filtrar_usuario(cpf, usuarios)
    
    if usuario:
        print('Já existe usuário com esse CPF!')
        return

    nome = input('Informe o nome completo: ')

    data_nascimento = input('Informe a data de nascimento (dd/mm/aaaa): ')

    while len(data_nascimento) != 10 or not data_nascimento[0:2].isdigit() or not data_nascimento[3:5].isdigit() or not data_nascimento[6:].isdigit() or data_nascimento[2] != '/' or data_nascimento[5] != '/':
        print('Formato de data inválido. Por favor, informe a data no formato dd/mm/aaaa.')
        data_nascimento = input('Informe a data de nascimento (dd/mm/aaaa): ')


    endereco = input('Informe o endereço (Rua, Nº, Bairro, Cidade/estado): ')

    usuarios.append({'Nome': nome, 'Data_Nascimento': data_nascimento, 'CPF': cpf, 'endereco': endereco})

    print('Usuário criado com sucesso!')


def filtrar_usuario(cpf, usuarios):

    for usuario in usuarios:

        if usuario['CPF'] == cpf:
            return usuario
        
    return None


def criar_conta(numero_conta, usuarios):

    cpf = input('Informe o CPF do Usuário: ')
    
    for usuario in usuarios:

        if usuario['CPF'] == int(cpf):
            print('Conta criada com sucesso!')
            return {'numero_conta': numero_conta, 'usuario': usuario}
    
    print('Usuário não encontrado. Criação de conta falhou!')
    return None


def listar_contas(contas):

    print("\n⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯Lista de contas⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯")

    for i, conta in enumerate(contas, start=1):
        print(f"Agência:\t{AGENCIA} ")
        print(f"C/C: \t\t{conta['numero_conta']}")
        print(f"Titular: \t{conta['usuario']['Nome']}")
        print("⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯")


def depositar():

    global saldo
    valor = float(input('Digite o valor do depósito: R$'))

    if valor > 0:
        saldo += valor
        extrato_lista.append(f'Depósito: R${valor:.2f}')
        print('Depósito realizado com sucesso!')

    else:
        print('Operação falhou! O valor informado é inválido.')

    return saldo, extrato_lista


def sacar():

    global saldo, numero_saques
    valor = float(input('Informe o valor que deseja sacar: R$'))

    execedeu_saldo = valor > saldo
    execedeu_limite = valor > limite
    execedeu_saques = numero_saques > LIMITE_SAQUES

    if execedeu_saldo:
        print('Operação Falhou! Você não tem saldo Suficiente.')
    
    elif execedeu_limite:
        print(f'Operação Falhou! O valor do saque execede o limite de {limite}')

    elif execedeu_saques:
        print('Operação Falhou! Limite de saque excedido.')

    elif valor > 0:
        saldo -= valor
        extrato_lista.append(f'Saque: R${valor:.2f}')
        numero_saques += 1
        print('Saque realizado com sucesso!')

    else:
        print('Operação falhou! Verifique seu saldo, limite ou número de saques.')

    return saldo, extrato_lista


def extrato():

    global saldo
    if not extrato_lista:
        print('Você não tem transações no momento.')

    else:
        print(f'⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯Extrato⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯')
        
        for transacao in extrato_lista:
            print(transacao)

        print(f'Saldo atual: R${saldo:.2f}')
        print(f'⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯')


def sair():
    msgsaida = 'SISTEMA_FINALIZADO'
    print(f'{msgsaida.center(42)}')

# Inicialização do programa
menu()
opcao_sistema()