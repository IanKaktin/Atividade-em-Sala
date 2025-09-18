# Atividade-em-Sala


Um pacote pode passar por vários momentos, como quando é recebido, vai para o armazém, sai para entrega, é entregue ou até perdido. Esses momentos são chamados de estados. Ao longo do caminho, acontecem eventos, como “chegou no armazém”,
“colocado no veículo” ou “foi entregue”.
A lógica é que cada estado do pacote já sabe como reagir a um evento, e cada evento também sabe o que fazer dependendo do estado em que o pacote está. Assim, não precisamos ficar usando vários “if” para verificar todas as combinações.
Para aplicar vários eventos de uma vez, usamos recursividade: pegamos o primeiro evento, aplicamos no pacote para gerar o próximo estado, e depois chamamos a mesma função de novo com o estado atualizado e o resto dos eventos.
No fim, o estado final do pacote mostra exatamente onde ele terminou.

from abc import ABC

# --- Classe base Estado ---
class Estado(ABC):
    transicoes = {}  # cada estado define suas transições

    def aplicar_evento(self, evento):
        # Se o evento existe nas transições, retorna o novo estado
        return self.transicoes.get(evento, self)()

# --- Estados concretos ---
class Recebido(Estado):
    transicoes = {
        "chegou_no_armazem": lambda: NoArmazem
    }

class NoArmazem(Estado):
    transicoes = {
        "colocado_no_veiculo": lambda: EmEntrega,
        "perdido": lambda: Perdido
    }

class EmEntrega(Estado):
    transicoes = {
        "foi_entregue": lambda: Entregue,
        "perdido": lambda: Perdido
    }

class Entregue(Estado):
    transicoes = {}  # estado final

class Perdido(Estado):
    transicoes = {}  # estado final

# --- Função recursiva ---
def processar_eventos(estado, eventos):
    if not eventos:
        return estado
    primeiro, *resto = eventos
    novo_estado = estado.aplicar_evento(primeiro)
    return processar_eventos(novo_estado, resto)
