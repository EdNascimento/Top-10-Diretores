# Top-10-Diretores
Análise dos diretores de cinema com maior bilheteria global usando Python
import matplotlib.pyplot as plt
from pandas import read_html

# Função para formatar os valores em milhões ou bilhões
def format_bilheteria(value):
    if value >= 1_000_000_000:
        return f"${value / 1_000_000_000:.1f}B"
    else:
        return f"${value / 1_000_000:.1f}M"

# Leitura dos dados
df = read_html("https://pt.wikipedia.org/wiki/Lista_de_filmes_de_maior_bilheteria")[0]

# Explodir diretores em linhas separadas
df["Diretor(a)"] = df["Diretor(a)"].str.split(" / ")
df = df.explode("Diretor(a)")

# Limpeza e conversão dos valores da bilheteria
df["Bilheteria (US$)"] = df["Bilheteria (US$)"].str.replace(" ", "")
df["Bilheteria (US$)"] = df["Bilheteria (US$)"].astype(int)

# Agrupamento por diretor e soma das bilheteiras
df = df.groupby("Diretor(a)")[["Bilheteria (US$)"]].sum()

# Selecionar os 10 diretores com maiores bilheteiras
df = df.sort_values("Bilheteria (US$)", ascending=False)[:10]
df = df.reset_index()

# Criar o gráfico
fig, ax = plt.subplots(figsize=(10, 6))  # Aumentando o tamanho do gráfico

# Título e rótulos
ax.set_title("Top 10 Diretores", fontsize=16)
ax.set_xlabel("Diretor(a)", fontsize=12)
ax.set_ylabel("Bilheteria Total", fontsize=12)

# Plotando as barras
bars = ax.bar(df.index, df["Bilheteria (US$)"], color='skyblue')

# Aplicando a função de formatação na exibição dos valores
ax.bar_label(bars, labels=[format_bilheteria(x) for x in df["Bilheteria (US$)"]], size=12)

# Ajustando os rótulos do eixo x
ax.set_xticks(df.index)
ax.set_xticklabels(df["Diretor(a)"], rotation=45, ha="right", fontsize=10)

# Ajustando grid e layout
ax.grid(axis="y", linestyle="--", alpha=0.7)
plt.tight_layout()

# Exibir o gráfico
plt.show()
