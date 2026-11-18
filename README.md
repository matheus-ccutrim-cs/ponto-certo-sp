# Ponto Certo SP

**Global Solution 2026.2 · Application Development · FIAP**

Matheus Costa Cutrim · RM 568087 · Turma 2CCPR · Professor Rodrigo Moraes  
Trabalho individual. Veja [PARTICIPANTES.txt](PARTICIPANTES.txt).

O Ponto Certo SP ajuda passageiros a decidir quando ir até um ponto de ônibus municipal de São Paulo. A busca distingue pontos com o mesmo nome pelo endereço e pelo código. O painel mostra a linha, o destino e a previsão de cada veículo, com as chegadas mais próximas primeiro. A consulta se renova a cada 30 segundos e também pode ser atualizada manualmente. O filtro de veículos acessíveis usa a indicação da SPTrans. Os pontos salvos ficam no navegador e podem ser consultados novamente sem repetir a busca.

## Rodar com dados reais

1. Instale o **Node.js 24** e abra um terminal na pasta que contém o `package.json`.
2. Na [área de desenvolvedores da SPTrans](https://www.sptrans.com.br/desenvolvedores/), cadastre ou acesse sua conta, vá a **Meus Aplicativos** e gere um token para o aplicativo. Aguarde a ativação se necessário.
3. Copie `.env.example` para `.env` e preencha `SPTRANS_TOKEN` com o token ativo.
4. Rode `npm start` e abra [localhost:3000](http://localhost:3000). Busque por **Paulista** e selecione um ponto.

O projeto usa recursos nativos do Node.js e funciona sem dependências npm. O arquivo `.env` é ignorado pelo Git. O token é usado apenas no código do servidor.

## Conferir o fluxo localmente

Rode `npm run demo` e abra [localhost:3000](http://localhost:3000). Busque por **Paulista** para ver previsões fictícias ou por **Praça da Sé** para conferir o estado sem chegadas. A página identifica a simulação e usa uma lista de favoritos própria. Rode `npm test` para testar validação de entrada, autenticação, cookies, renovação de sessão e cálculo de chegada.

## Como o código funciona

| Arquivo | Responsabilidade |
| --- | --- |
| `public/index.html` e `public/style.css` | Interface em português, layout responsivo, campos rotulados e controles por teclado |
| `public/app.js` | Busca, seleção do ponto, estados da interface, favoritos e atualização periódica |
| `public/previsoes.js` | Converte a resposta da SPTrans em uma lista ordenada por tempo de chegada |
| `api/olho-vivo.js` | Função da Vercel que recebe as consultas do navegador |
| `lib/request.js` e `lib/olho-vivo.js` | Validam os parâmetros, autenticam na SPTrans e encaminham as consultas |
| `scripts/dev.js` | Servidor local e dados fictícios para a simulação |
| `test/project.test.js` | Testes automatizados com respostas controladas |

1. O navegador chama `/api/olho-vivo` no domínio do app.
2. O servidor autentica via `POST /Login/Autenticar`, guarda os cookies da sessão na instância e os envia às consultas seguintes. Se a sessão retornar 401 ou 403, autentica novamente e repete a consulta uma vez.
3. O proxy chama `GET /Parada/Buscar` para localizar pontos ou `GET /Previsao/Parada` para consultar chegadas pelo código do ponto.
4. O JavaScript reúne os veículos das linhas e ordena pela diferença entre o horário previsto e o horário de referência da SPTrans. Por exemplo, de 23:58 até 00:05 são sete minutos. O app mostra a hora prevista e o horário de referência da consulta.

Dados e previsões fornecidos pela [SPTrans · API Olho Vivo](https://www.sptrans.com.br/desenvolvedores/api-do-olho-vivo-guia-de-referencia/documentacao-api).

## Publicar e entregar

1. Extraia o ZIP. No GitHub, crie um repositório, por exemplo `ponto-certo-sp`. Envie **o conteúdo** da pasta `ponto-certo` de modo que `README.md`, `PARTICIPANTES.txt` e `vercel.json` fiquem na raiz. Se usar a interface do GitHub, lembre-se de incluir `.gitignore` e `.env.example`. Mantenha `.env` apenas no computador.
2. Entre na [Vercel](https://vercel.com/) com o GitHub. Abra **Add New → Project**, escolha o repositório e clique em **Import**.
3. Use **Other** como Framework Preset e a raiz do repositório como Root Directory. A configuração do projeto serve `public/` como arquivos estáticos e `api/` como função Node.js.
4. Em **Environment Variables**, adicione `SPTRANS_TOKEN` com o valor ativo no ambiente **Production** e clique em **Deploy**. Se configurar a variável depois do primeiro deploy, faça um novo deploy.
5. Abra a URL pública em janela anônima. Busque **Paulista**, selecione um ponto e confira se aparecem previsões reais. Se um ponto estiver sem chegadas, experimente outro ponto ativo. Teste também no celular e confirme que o repositório está acessível para o professor.
6. Envie à FIAP **a URL do repositório GitHub e a URL pública da Vercel**. O README e o arquivo de contribuições já fazem parte do projeto.

## Critérios de avaliação

| Critério | Como conferir |
| --- | --- |
| Aplicação rodando com dados reais (3 pts) | Abra o deploy público e consulte um ponto usando o token ativo |
| Qualidade do código (3 pts) | Confira a divisão de responsabilidades acima e execute `npm test` |
| Usabilidade e responsividade (2 pts) | Faça uma busca no celular, use o filtro e navegue pelos controles com Tab |
| Utilidade (2 pts) | Identifique o próximo veículo de um ponto e salve o local para consultar depois |
