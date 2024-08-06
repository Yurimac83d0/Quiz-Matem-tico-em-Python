# Quiz-Matem-tico-em-Python
import time
import threading
import random

def soma(a, b):
    if b == 0:
        return a
    return soma(a + 1, b - 1)

def subtracao(a, b):
    if b == 0:
        return a
    return subtracao(a - 1, b - 1)

def multiplicacao(a, b):
    if b == 0:
        return 0
    return soma(a, multiplicacao(a, b - 1))

def elevado(a, b):
    if b == 0:
        return 1
    return multiplicacao(a, elevado(a, b - 1))

def fatorial(n):
    if n == 0:
        return 1
    return multiplicacao(n, fatorial(n - 1))

def divisao_chao(a, b):
    if b == 0:
        raise ValueError("Divisão por zero não é permitida.")
    if a < b:
        return 0
    return soma(1, divisao_chao(subtracao(a, b), b))

def divisao_teto(a, b):
    if b == 0:
        raise ValueError("Divisão por zero não é permitida.")
    if a <= b:
        return 1
    return soma(1, divisao_teto(subtracao(a, b - 1), b))

def percentual(a, b):
    return divisao_chao(multiplicacao(a, b), 100)

def resto_divisao(a, b):
    if a < b:
        return a
    return resto_divisao(subtracao(a, b), b)

def maximo(a, b):
    if a >= b:
        return a
    return b

def minimo(a, b):
    if a <= b:
        return a
    return b

def igual(a, b):
    if a == b:
        return True
    return False

def diferente(a, b):
    return not igual(a, b)

def gerar_pergunta():
    operacoes = ['+', '-', '*', '**', '!', '//', '%']
    op = random.choice(operacoes)
    if op == '+':
        x = random.randint(1, 10)
        y = random.randint(1, 10)
        return f"Qual é a soma de {x} + {y}?", soma(x, y)
    elif op == '-':
        x = random.randint(1, 10)
        y = random.randint(1, 10)
        return f"Qual é a subtração de {x} - {y}?", subtracao(x, y)
    elif op == '*':
        x = random.randint(1, 10)
        y = random.randint(1, 10)
        return f"Qual é a multiplicação de {x} * {y}?", multiplicacao(x, y)
    elif op == '**':
        x = random.randint(1, 5)
        y = random.randint(1, 3)
        return f"Qual é {x} elevado a {y}?", elevado(x, y)
    elif op == '!':
        x = random.randint(1, 5)
        return f"Qual é o fatorial de {x}?", fatorial(x)
    elif op == '//':
        x = random.randint(1, 10)
        y = random.randint(1, 10)
        while y == 0:
            y = random.randint(1, 10)
        return f"Qual é a divisão chão de {x} // {y}?", divisao_chao(x, y)
    elif op == '%':
        x = random.randint(1, 10)
        y = random.randint(1, 100)
        return f"Qual é {x}% de {y}?", percentual(x, y)

def jogador_responde(jogador, pergunta, resposta):
    resposta_jogador = [None]
    tempo_restante = [30]

    def timer():
        for t in range(45):
            time.sleep(1)
            tempo_restante[0] -= 1
            if resposta_jogador[0] is not None:
                break
            print(f"Tempo restante: {tempo_restante[0]} segundos", end='\r')

    thread_timer = threading.Thread(target=timer)
    thread_timer.start()

    try:
        resposta_jogador[0] = int(input(f"Jogador {jogador+1}: {pergunta}\nSua resposta: "))
    except ValueError:
        resposta_jogador[0] = None

    thread_timer.join()

    if resposta_jogador[0] == resposta:
        return True
    else:
        return False

def jogar_quiz():
    num_jogadores = int(input("Informe o número de jogadores (2 ou 3): "))
    jogadores = [0] * num_jogadores
    partidas = 5

    for rodada in range(partidas):
        for i in range(num_jogadores):
            pergunta, resposta = gerar_pergunta()
            if jogador_responde(i, pergunta, resposta):
                jogadores[i] += 1

        max_pontuacao = max(jogadores)
        min_pontuacao = min(jogadores)
        print(f"Pontuação máxima: {max_pontuacao}")
        print(f"Pontuação mínima: {min_pontuacao}")

    ranking = sorted(enumerate(jogadores), key=lambda x: x[1], reverse=True)
    print("Ranking final:")
    for pos, (jogador, pontuacao) in enumerate(ranking, 1):
        print(f"{pos}º lugar: Jogador {jogador + 1} com {pontuacao} pontos")

if __name__ == "__main__":
    jogar_quiz()
