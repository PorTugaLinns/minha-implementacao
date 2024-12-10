import csv
from datetime import datetime
from collections import defaultdict

class ProdutoDTO:
    def _init_(self, ativo, quantidade, preco, data, descricao, estoque_minimo):
        self._ativo = ativo              # Booleano
        self._quantidade = quantidade    # Inteiro
        self._preco = preco              # Float
        self._data = data                # String
        self._descricao = descricao      # String
        self._estoque_minimo = estoque_minimo  # Inteiro

# Exemplo de dados
produtos = [
    ProdutoDTO(True, 100, 10.5, "2024-12-01", "Produto A", 10),
    ProdutoDTO(False, 50, 20.0, "2024-12-01", "Produto B", 5),
    ProdutoDTO(True, 200, 30.5, "2024-12-02", "Produto C", 20),
    ProdutoDTO(False, 30, 40.0, "2024-12-02", "Produto A", 15)
]

def produto_to_dict(produto):
    # Usando vars() para obter o dicionário de atributos do objeto
    atributos_dict = vars(produto)
    # Remover underscores dos nomes dos atributos
    atributos_filtrados = {attr.lstrip('_'): getattr(produto, attr) for attr in atributos_dict.keys()}
    return atributos_filtrados

def gerar_csv_dinamico(produtos, nome_arquivo):
    # Converter a lista de objetos em uma lista de dicionários
    lista_dict = [produto_to_dict(produto) for produto in produtos]

    # Obter os nomes dos campos a partir das chaves do primeiro dicionário
    if lista_dict:
        campos = lista_dict[0].keys()

        with open(nome_arquivo, mode='w', newline='', encoding='utf-8') as file:
            writer = csv.DictWriter(file, fieldnames=campos)
            writer.writeheader()  # Escrever o cabeçalho

            for item in lista_dict:
                writer.writerow(item)
                # Debug print para verificar o que está sendo escrito
                print(f"Escrevendo linha: {item}")

        print(f"Arquivo CSV '{nome_arquivo}' gerado com sucesso!")
    else:
        print("A lista de produtos está vazia. Nenhum arquivo CSV foi gerado.")

def gerar_csv_por_mes(produtos):
    produtos_por_mes = defaultdict(list)
    for produto in produtos:
        mes = datetime.strptime(produto._data, '%Y-%m-%d').strftime('%Y-%m')
        produtos_por_mes[mes].append(produto)

    for mes, items in produtos_por_mes.items():
        gerar_csv_dinamico(items, f'produtos_{mes}.csv')

# Gerar o arquivo CSV com dados de produtos
print("Gerando arquivo CSV por dia...")
gerar_csv_dinamico(produtos, 'produtos.csv')

# Gerar o arquivo CSV agrupado por mês
print("Gerando arquivos CSV por mês...")
gerar_csv_por_mes(produtos)
