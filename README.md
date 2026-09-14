# 📊 Sistema de Monitoramento de Ativos de TI e Chamados (Dashboard Executivo)

> **Projeto de Portfólio Integrado:** Administração & Análise e Desenvolvimento de Sistemas

## 📋 Sobre o Projeto
Este projeto une **Análise de Sistemas** (modelagem de banco de dados SQL e análise de métricas) com **Administração** (indicadores de desempenho KPIs, custos operacionais e gestão do nível de serviço - SLA).

O objetivo foi resolver a falta de visibilidade sobre os custos de manutenção de TI e o cumprimento de SLAs de suporte em uma empresa, transformando dados brutos de infraestrutura em insights para tomada de decisão estratégica.

---

## 🛠️ Tecnologias e Conceitos Utilizados
- **Análise de Sistemas:** SQL (PostgreSQL/MySQL), Modelagem Relacional de Dados, Análise de Dados.
- **Administração:** Governança de TI (ITIL/COBIT), Gestão de Custos, Métricas de Desempenho (KPIs: SLA, TMT, FCR).

---

## 🗄️ Estrutura do Banco de Dados (SQL)

```sql
-- Criação da tabela de Ativos de TI
CREATE TABLE Ativos_TI (
    ID_Ativo INT PRIMARY KEY,
    Equipamento VARCHAR(50),
    Setor VARCHAR(50),
    Custo_Aquisicao DECIMAL(10,2),
    Status VARCHAR(20)
);

-- Criação da tabela de Chamados
CREATE TABLE Chamados_Suporte (
    ID_Chamado INT PRIMARY KEY,
    ID_Ativo INT,
    Tipo_Problema VARCHAR(50),
    Tempo_Horas_Resolucao INT,
    SLA_Cumprido CHAR(3),
    Custo_Manutencao DECIMAL(10,2),
    FOREIGN KEY (ID_Ativo) REFERENCES Ativos_TI(ID_Ativo)
);

-- Consulta Analítica para consolidação de KPIs por setor
SELECT 
    a.Setor,
    COUNT(c.ID_Chamado) AS Total_Chamados,
    AVG(c.Tempo_Horas_Resolucao) AS Tempo_Medio_Resolucao_Horas,
    SUM(c.Custo_Manutencao) AS Custo_Total_Manutencao,
    ROUND((SUM(CASE WHEN c.SLA_Cumprido = 'SIM' THEN 1 ELSE 0 END) * 100.0 / COUNT(c.ID_Chamado)), 2) AS Perc_SLA_Atendido
FROM Chamados_Suporte c
JOIN Ativos_TI a ON c.ID_Ativo = a.ID_Ativo
GROUP BY a.Setor;
