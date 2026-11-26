# **MONITOR-MOTORES-PYTHON**
Esse projeto foi feito para _monitorar motores industriais._
Esse código mostra a temperatura, corrente, vibração, rpm, alertas e potência
***
## Definindo banco de dados
1. Nas primeiras linhas de código, eu importo uma biblioteca chamada datetime que está dentro de outra biblioteca chamada datetime `from datetime import datetime`.
    ***
1. Depois eu nomeio o nome do sistema e a versão.
    ```py
    SISTEMA_NOME = "Monitoramento de Motores Industriais"
    VERSAO = "1.0.0"
    ```
    ***
1. Após isso, eu crio uma lista com os limites máximos de temperatura, corrente, vibração e rpm: 
    ```py
        LIMITES = {
            "temperatura_max": 80.0,
            "corrente_max": 18.5,
            "vibracao_max": 4.5,
            "rpm_min": 1400,
            "rpm_max": 1800,
        }
    ```
    ***
    
1. Depois eu crio um dicionário definindo o nome dos motores, setor, temperatura, corrente, vibração, rpm e potencia.
    >OBS: Essa parte vai ser muito importante para o desenvolvimento do projeto, pois nesse dicionário contém todas as informações dos 4 motores.
    ```py
    LEITURAS_MOTORES = [
    {"id": "MTR-01", "setor": "Estamparia", "temperatura": 68.4, "corrente": 14.2, "vibracao": 2.1, "rpm": 1750, "potencia_kw": 7.5},
    {"id": "MTR-02", "setor": "Usinagem", "temperatura": 84.7, "corrente": 19.3, "vibracao": 5.2, "rpm": 1690, "potencia_kw": 11.0},
    {"id": "MTR-03", "setor": "Bobinagem", "temperatura": 76.2, "corrente": 17.8, "vibracao": 4.7, "rpm": 1520, "potencia_kw": 5.5},
    {"id": "MTR-04", "setor": "Pintura", "temperatura": 61.0, "corrente": 11.5, "vibracao": 1.8, "rpm": 1780, "potencia_kw": 7.5},
    ]
    ```
    ***
## Criando funções
1. **A primeira função criada foi para validar os dados do dicionário `leituras_motores`:

    ```py
    def validar_leitura(motor):
        campos_obrigatorios = ["id", "setor", "temperatura", "corrente", "vibracao", "rpm", "potencia_kw"]
        for campo in campos_obrigatorios:
            if campo not in motor or motor[campo] is None:
                return False
        if motor["temperatura"] < 0 or motor["corrente"] < 0 or motor["vibracao"] < 0 or motor["rpm"] < 0:
            return False
        return True
    ```
    A primeira linha ele define os dados obrigatórios, sendo o id, setor, temperatura, corrente, vibração, rpm e potencia. 
    Depois ele verifica se esses dados obrigatórios estão dentro do dicionário `leituras_motores`. Caso algum não esteja, ele mostra erro no terminal.
    ***
1. Depois, tem uma função que analisa os alertas:
    ```py
    def analisar_alertas(motor):
        alertas = []
        if motor["temperatura"] > LIMITES["temperatura_max"]:
            alertas.append(f"Superaquecimento ({motor['temperatura']:.1f} C)")
        if motor["corrente"] > LIMITES["corrente_max"]:
            alertas.append(f"Sobrecorrente ({motor['corrente']:.1f} A)")
        if motor["vibracao"] > LIMITES["vibracao_max"]:
            alertas.append(f"Vibracao elevada ({motor['vibracao']:.1f} mm/s)")
        if motor["rpm"] < LIMITES["rpm_min"] or motor["rpm"] > LIMITES["rpm_max"]:
            alertas.append(f"Rotacao fora da faixa ({motor['rpm']} RPM)")

        if len(alertas) == 0:
            status = "NORMAL"
        elif len(alertas) == 1:
            status = "ATENCAO"
        else:
            status = "CRITICO"

        return status, alertas
    ```
    Ele vê a temperatura que está no dicionário `leituras_motores` e verifica se ela está dentro do limite da lista `limites`.
     Ele verifica a temperatura, corrente, vibração e rpm, caso tenha algo acima do valor máximo, ele adiciona o motor numa lista chamada `alertas`.
    Depois ele verifica a quantidade de itens dentro da lista `alertas`, caso não tenha nada, a variavel `status` ficará como "normal", caso tenha um item, a variavel `status` ficará como "atencao", caso tenha mais de um item, ele marcará como "critico".
    ***
1. Depois ele calcula a temperatura média, corrente média, potencia total e a maior temperatura registrada.
    ```py
    def calcular_estatisticas(motores):
        total = len(motores)
        if total == 0:
            return {}

        temp_media = sum(m["temperatura"] for m in motores) / total
        corrente_media = sum(m["corrente"] for m in motores) / total
        potencia_total = sum(m["potencia_kw"] for m in motores)
        maior_temp = max(motores, key=lambda m: m["temperatura"])

        return {
            "total_motores": total,
            "temperatura_media": round(temp_media, 2),
            "corrente_media": round(corrente_media, 2),
            "potencia_total_kw": round(potencia_total, 2),
            "motor_mais_quente": maior_temp["id"],
        }
    ```
1. A última função é para exibir o painel.
Nela aparece o nome do sistema, a versão, Data e hora da leitura (por isso importamos a biblioteca `datetime`). Depois ele imprime os motores que tiveram alertas, logo em seguida as estatisticas calculadas no tópico anterior.

# Resumo
Por fim, esse programa ele faz:
- Mostra todos os motores, íncluindo o nível de alerta, temperatura, corrente, vibração e rpm. 
- Caso o motor tenha um nível de alerta diferente de "normal", ele mostra aonde está o alerta do motor.
- Mostra o resumo dos 4 motores, contendo a quantidade de motores, temperatura média, corrente média, potência total e maior temperatura.