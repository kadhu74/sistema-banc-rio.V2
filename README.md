# sistema-banc-rio.V2
# Sistema Bancário Modularizado

usuarios = []  # Lista para armazenar os usuários
contas = []  # Lista para armazenar as contas
AGENCIA = '0001'

# Função para exibir menu

def menu():
    while True:
        print('\n==== MENU BANCO ===')
        print('1 - Criar Usuário')
        print('2 - Criar Conta Corrente')
        print('3 - Depósito')
        print('4 - Saque')
        print('5 - Extrato')
        print('6 - Sair')
        opcao = input('Escolha uma opção: ')

        if opcao == '1':
            nome = input('Nome: ')
            data_nascimento = input('Data de Nascimento (dd/mm/aaaa): ')
            cpf = input('CPF (apenas números): ')
            endereco = input('Endereço (logradouro, nro - bairro - cidade/sigla estado): ')
            criar_usuario(nome, data_nascimento, cpf, endereco)
        elif opcao == '2':
            cpf = input('CPF do usuário (apenas números): ')
            criar_conta(cpf)
        elif opcao == '3':
            numero_conta = int(input('Número da conta: '))
            valor = float(input('Valor do depósito: '))
            depositar(contas[numero_conta - 1], valor)
        elif opcao == '4':
            numero_conta = int(input('Número da conta: '))
            valor = float(input('Valor do saque: '))
            sacar(conta=contas[numero_conta - 1], valor=valor)
        elif opcao == '5':
            numero_conta = int(input('Número da conta: '))
            exibir_extrato(contas[numero_conta - 1])
        elif opcao == '6':
            print('Obrigado por usar o nosso banco!')
            break
        else:
            print('Opção inválida, tente novamente.')

menu()

# Função para criar um novo usuário

def criar_usuario(nome, data_nascimento, cpf, endereco):
    # Verifica se o CPF já está cadastrado
    for usuario in usuarios:
        if usuario['cpf'] == cpf:
            print('Erro: CPF já cadastrado. Não é possível criar o usuário.\n')
            return
    # Cria o usuário e adiciona à lista de usuários
    usuario = {
        'nome': nome,
        'data_nascimento': data_nascimento,
        'cpf': cpf,
        'endereco': endereco
    }
    usuarios.append(usuario)
    print(f'Usuário {nome} criado com sucesso!\n')

# Função para criar uma nova conta corrente

def criar_conta(cpf):
    # Verifica se o CPF está cadastrado
    usuario_encontrado = None
    for usuario in usuarios:
        if usuario['cpf'] == cpf:
            usuario_encontrado = usuario
            break
    if not usuario_encontrado:
        print('Erro: Usuário não encontrado. Não é possível criar a conta.\n')
        return
    # Cria a conta com número sequencial
    numero_conta = len(contas) + 1
    conta = {
        'agencia': AGENCIA,
        'numero_conta': numero_conta,
        'usuario': usuario_encontrado,
        'saldo': 0.0,
        'extrato': []
    }
    contas.append(conta)
    print(f'Conta número {numero_conta} criada com sucesso para o usuário {usuario_encontrado["nome"]}!\n')

# Função para depositar (positional only)

def depositar(conta, valor, /):
    if valor <= 0:
        print('Erro: O valor do depósito deve ser positivo.\n')
        return
    conta['saldo'] += valor
    conta['extrato'].append(f'Depósito: +R${valor:.2f}')
    print(f'Depósito de R${valor:.2f} realizado com sucesso!\n')

# Função para sacar (keyword only)

def sacar(*, conta, valor, limite=500, limite_saques=3):
    if valor <= 0:
        print('Erro: O valor do saque deve ser positivo.\n')
        return
    if valor > conta['saldo']:
        print('Erro: Saldo insuficiente.\n')
        return
    if valor > limite:
        print('Erro: O valor do saque excede o limite permitido.\n')
        return
    if conta['extrato'].count('Saque') >= limite_saques:
        print('Erro: Número máximo de saques diários excedido.\n')
        return
    conta['saldo'] -= valor
    conta['extrato'].append('Saque')
    print(f'Saque de R${valor:.2f} realizado com sucesso!\n')

# Função para exibir extrato (positional and keyword)

def exibir_extrato(conta, /, *, incluir_saldo=True):
    print('\nExtrato da Conta:')
    for item in conta['extrato']:
        print(item)
    if incluir_saldo:
        print(f'Saldo atual: R${conta["saldo"]:.2f}\n')
