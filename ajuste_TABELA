# streamlit_app.py
import streamlit as st
import pandas as pd
import io
import re
from datetime import datetime, timedelta

st.title("Gerador de Tabela Padrão - CVE")

# Upload dos arquivos
aprovados_file = st.file_uploader("📄 Envie o arquivo APROVADOS (.xlsx ou .csv)", type=["xlsx", "csv"])
resultados_file = st.file_uploader("📄 Envie o arquivo RESULTADOS (.xlsx ou .csv)", type=["xlsx", "csv"])

def cpf_limpo(cpf):
    return re.sub(r'\D', '', str(cpf)).zfill(11)

def dia_juliano(data):
    return data.strftime('%j')

def format_data(data):
    return data.strftime('%Y%m%d')

def format_categoria(cat):
    return str(cat).strip().ljust(4)

def gerar_certificado(i):
    return f'escola{i:09}'

def data_mais_5_anos(data):
    return (data + timedelta(days=5*365)).strftime('%Y%m%d')

if aprovados_file and resultados_file:
    # Leitura dos dados
    aprovados = pd.read_excel(aprovados_file) if aprovados_file.name.endswith('.xlsx') else pd.read_csv(aprovados_file)
    resultados = pd.read_excel(resultados_file) if resultados_file.name.endswith('.xlsx') else pd.read_csv(resultados_file)

    nova = pd.DataFrame()
    nova['nu-seq-trans'] = [str(200001 + i) for i in range(len(aprovados))]
    nova['cod-trans'] = '181'
    nova['cod-mod-trans'] = '7'
    nova['codusu'] = aprovados['CPF'].apply(cpf_limpo)
    nova['uf-or-trans'] = 'SA'
    nova['uf-orig-transm'] = 'SA'
    nova['uf-des-transm'] = 'BR'
    nova['cond-trans'] = '0'
    nova['tam-trans'] = '0152'
    nova['cod-ret-trans'] = '00'
    nova['dia-juliano'] = aprovados['Data de matrícula'].apply(lambda x: dia_juliano(pd.to_datetime(x)))
    nova['tipo-chave'] = '2'
    nova['numero-cnh'] = resultados['Nº Registro da CNH:'].astype(str).apply(lambda x: re.sub(r'\D', '', x))
    nova['tipo-evento'] = 'C'
    nova['tipo-atualizacao'] = 'I'
    nova['codigo-curso'] = '04'
    nova['modalidade'] = '2'
    nova['Numero Certificado'] = [gerar_certificado(i) for i in range(1, len(aprovados)+1)]
    nova['data-inicio-curso'] = aprovados['Data de matrícula'].apply(lambda x: format_data(pd.to_datetime(x)))
    nova['data-fim-curso'] = aprovados['Data de conclusão'].apply(lambda x: format_data(pd.to_datetime(x)))
    nova['carga-horaria'] = '060'
    nova['cnpj-entidade-crede'] = '00394494000560'
    nova['cpf-instrutor'] = '57437670097'
    nova['municipio-curso'] = '09701'
    nova['uf-curso'] = 'DF'
    nova['data-validade'] = aprovados['Data de matrícula'].apply(lambda x: data_mais_5_anos(pd.to_datetime(x)))
    nova['categoria'] = resultados['Categoria da CNH:'].apply(format_categoria)
    nova['observacoes-curso'] = '99' + ' ' * 18

    st.success("✅ Tabela gerada com sucesso! Veja a prévia abaixo:")
    st.dataframe(nova.head())

    csv = nova.to_csv(index=False).encode('utf-8')
    st.download_button(
        label="📥 Baixar CSV",
        data=csv,
        file_name='tabela_padrao_processada.csv',
        mime='text/csv',
    )
