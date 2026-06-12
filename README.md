# RPA Audit Data Extraction: Power Automate + Python + SQLite

[![Microsoft Learn](https://img.shields.io/badge/Microsoft%20Learn-Estudos%20RPA-blue?style=flat-square&logo=microsoft)](https://learn.microsoft.com/pt-br/users/williandiasdeoliveira-5456/)
[![Portfolio](https://img.shields.io/badge/Portf%C3%B3lio-Acessar-green?style=flat-square)](https://willian-dias-oliveira.vercel.app/)

Este projeto é uma solução completa de automação de processos robóticos (RPA) desenvolvida de ponta a ponta. O robô gerencia uma fila de auditoria complexa com 200 CNPJs, realiza o enriquecimento de dados via consumo de API e atualiza os registros de forma segura diretamente em um banco de dados local.

O fluxo combina a facilidade de orquestração do **Power Automate Desktop (Power Fx)**, a flexibilidade do **Python** para processamento de estruturas e a robustez do **SQL (SQLite)** para a persistência de dados.

---

## 🛠️ Tecnologias e Conceitos Utilizados

* **Orquestrador:** Microsoft Power Automate Desktop (Fluxo habilitado com suporte a Power Fx)
* **Banco de Dados:** SQLite (Comandos DDL e DML: `SELECT`, `UPDATE`, restrições de tabelas e chaves primárias)
* **Linguagem de Suporte:** Python 3 (Manipulação de strings, argumentos de sistema `sys.argv`, e tratamento de dados com estruturas `try/except`)
* **Integração:** Consumo de APIs REST (Requisições HTTP `GET` via *Invoke Web Service* com tratamento de Status Codes)
* **Padrões de Projeto:** Arquitetura orientada a filas (*Queue-Based RPA*), tratamento de erros e resiliência de banco (*Database Locking prevention*).

---

## 📐 Arquitetura da Solução (Passo a Passo)

1.  **Leitura da Fila:** O robô abre uma conexão com o banco de dados `banco_auditoria.db` e executa uma query SQL filtrando apenas os registros necessários:
    ```sql
    SELECT * FROM Auditoria WHERE Status = 'Pendente';
    ```
2.  **Processamento em Loop:** Através de uma estrutura `For Each`, o robô isola o CNPJ da linha atual e constrói dinamicamente uma chamada para a **Brasil API**:
    ```text
    [https://brasilapi.com.br/api/cnpj/v1/](https://brasilapi.com.br/api/cnpj/v1/) [CNPJ_ATUAL]
    ```
3.  **Tratamento de Exceções:** Uma condicional Power Fx (`=StatusCode = 200`) garante que o robô só processe dados válidos. Caso a API retorne um erro (como CNPJ inválido/Status 400), o registro é ignorado para preservar a execução da fila.
4.  **Extração de Dados:** O Power Automate converte a resposta bruta e extrai os campos `razao_social` e `descricao_situacao_cadastral`.
5.  **Persistência e Fechamento:** O robô executa um comando de atualização concatenado via Power Fx para modificar o status do item para "Concluído", salvando os dados e fechando a conexão de forma limpa:
    ```sql
    ="UPDATE Auditoria SET Status = 'Concluido', Razao_Social = '" & RazaoSocial_Limpa & "', Situacao_Receita = '" & JsonAsCustomObject.descricao_situacao_cadastral & "' WHERE CNPJ = '" & CnpjAtual & "';"
    ```

---

## 🚀 Como Executar o Projeto

1.  Clone este repositório em sua máquina.
2.  Certifique-se de possuir o **Power Automate Desktop** com suporte a fluxos Power Fx ativado.
3.  Insira o arquivo do banco de dados `banco_auditoria.db` no diretório configurado.
4.  Execute o fluxo através do console do Power Automate.

*Nota técnica para auditoria visual:* Para monitorar a execução em tempo real pelo software DB Browser for SQLite sem bloquear os comandos de gravação do robô, abra o arquivo utilizando o modo **"Open Database Read Only..."** (Somente Leitura) e utilize a tecla `F5` para atualizar a tabela dinamicamente.

---

## 🎯 Sobre Mim e Contato

Este projeto faz parte do meu portfólio de estudos contínuos focado em Engenharia de Automação e RPA. Toda a fundamentação de lógica e ferramentas utilizadas foi consolidada através das minhas trilhas oficiais de aprendizado.

* 💻 **Meu Portfólio Web:** [willian-dias-oliveira.vercel.app](https://willian-dias-oliveira.vercel.app/)
* 📚 **Perfil Oficial de Estudos no Microsoft Learn:** [Acessar Trilha de Aprendizado](https://learn.microsoft.com/pt-br/users/williandiasdeoliveira-5456/)
* 💼 **Conecte-se comigo no LinkedIn:** https://www.linkedin.com/in/willian-dias-oliveira/
