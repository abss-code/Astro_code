# 1. INSTALAÇÃO DAS FERRAMENTAS E DO CLOUDFLARE
print("Instalando dependências... Aguarde.")
!pip install -q streamlit plotly numpy
!wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
!dpkg -i cloudflared-linux-amd64.deb > /dev/null

# 2. CRIAÇÃO DO ARQUIVO DO SIMULADOR (app.py) COM GAIOLA ESFÉRICA 3D
print("Criando o aplicativo do simulador...")
with open("app.py", "w") as f:
    f.write('''import streamlit as st
import numpy as np
import plotly.graph_objects as go

st.set_page_config(layout="wide")
st.title("🌌 Simulador de Sistema Planetário 3D")

st.sidebar.header("Parâmetros do Sistema")
sistema = st.sidebar.selectbox("Escolha o Sistema:", ["LHS 1140", "Personalizado"])

if sistema == "LHS 1140":
    raio_c = 0.0270
    raio_b = 0.0936
    hz_interna = 0.063
    hz_externa = 0.091
else:
    raio_c = st.sidebar.slider("Órbita Planeta C (UA)", 0.01, 0.2, 0.03)
    raio_b = st.sidebar.slider("Órbita Planeta B (UA)", 0.05, 0.5, 0.10)
    hz_interna = st.sidebar.slider("Início da Zona Habitável (UA)", 0.01, 0.3, 0.06)
    hz_externa = st.sidebar.slider("Fim da Zona Habitável (UA)", 0.05, 0.5, 0.09)

theta = np.linspace(0, 2 * np.pi, 200)
fig = go.Figure()

# 1. Estrela Central
fig.add_trace(go.Scatter3d(
    x=[0], y=[0], z=[0], mode='markers',
    marker=dict(size=14, color='orange', symbol='circle'), name="Estrela"
))

# 2. Órbita C (Plano Z = 0)
fig.add_trace(go.Scatter3d(
    x=raio_c * np.cos(theta), y=raio_c * np.sin(theta), z=np.zeros_like(theta),
    mode='lines', line=dict(color='lightgreen', width=3, dash='dash'), name="LHS 1140 c"
))

# 3. Órbita B (Plano Z = 0)
fig.add_trace(go.Scatter3d(
    x=raio_b * np.cos(theta), y=raio_b * np.sin(theta), z=np.zeros_like(theta),
    mode='lines', line=dict(color='coral', width=3), name="LHS 1140 b"
))

# 4. Construção da Zona Habitável Esférica (Gaiola de Anéis Volumétricos)
# Desenha camadas em várias latitudes para formar uma casca esférica visível de todos os lados
raio_hz = (hz_interna + hz_externa) / 2
camadas_z = np.linspace(-raio_hz * 0.9, raio_hz * 0.9, 15)

for z in camadas_z:
    # Ajusta o raio do anel para cada altura Z usando Pitágoras para manter esférico
    raio_anel = np.sqrt(max(0, raio_hz**2 - z**2))
    fig.add_trace(go.Scatter3d(
        x=raio_anel * np.cos(theta), y=raio_anel * np.sin(theta), z=np.ones_like(theta) * z,
        mode='lines', line=dict(color='rgba(46, 139, 87, 0.25)', width=2),
        showlegend=False
    ))

# Legenda bonitinha para a Esfera Habitável
fig.add_trace(go.Scatter3d(
    x=[None], y=[None], z=[None], mode='lines',
    line=dict(color='rgba(46, 139, 87, 0.7)', width=6), name="Zona Habitável (Esfera)"
))

# Ajustes de Layout e proporções estritas de Cubo 1:1:1
fig.update_layout(
    template="plotly_dark",
    scene=dict(
        xaxis=dict(title='X (UA)', range=[-0.12, 0.12]),
        yaxis=dict(title='Y (UA)', range=[-0.12, 0.12]),
        zaxis=dict(title='Z (UA)', range=[-0.12, 0.12]),
        aspectmode='cube'  # Força os eixos a terem comprimentos visuais idênticos
    ),
    margin=dict(l=0, r=0, b=0, t=40), height=650
)

st.plotly_chart(fig, use_container_width=True)
''')

# 3. INICIALIZAÇÃO DO SERVIDOR E DO TÚNEL
print("\nIniciando o servidor... Procure pelo link '.trycloudflare.com' abaixo e clique nele!\n")
!streamlit run app.py & cloudflared tunnel --url http://localhost:8501
