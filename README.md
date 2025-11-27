*Requisitos*
*Requisitos funcionais*

• 	Cadastro de usuários: Criar, editar, desativar e listar usuários (aluno, professor, bibliotecário).
• 	Autenticação e perfis: Login e controle de acesso por perfil (usuário vs. bibliotecário).
• 	Catálogo de livros: Cadastrar, editar, desativar, listar e buscar livros por título, autor, assunto e ISBN.
• 	Empréstimos: Registrar empréstimo, calcular data de devolução, impedir duplicidade se não houver exemplares.
• 	Devoluções: Registrar devolução, calcular multas por atraso e atualizar disponibilidade.
• 	Reservas: Permitir reservas quando não há exemplares disponíveis, com fila por prioridade temporal.
• 	Renovações: Permitir renovação se não houver reservas pendentes, respeitando limite de renovações.
• 	Notificações: Alertas de vencimento, disponibilidade de reserva e multas pendentes (simulado).
• 	Relatórios: Empréstimos ativos, atrasos, histórico por usuário, livros mais emprestados.
• 	Administração: Gerenciar exemplares, categorias/assuntos, políticas (prazos, multas).

*Requisitos não funcionais*

• 	Usabilidade: Interface clara, busca acessível e feedback imediato.
• 	Segurança: Perfis e regras de acesso; registro de operações críticas.
• 	Desempenho: Busca no catálogo em tempo razoável (milhares de itens).
• 	Confiabilidade: Regras de consistência para empréstimos, reservas e multas.
• 	Escalabilidade: Estrutura preparada para futura persistência em SGBD.
• 	Mantenabilidade: Código modular, testes automatizados e documentação clara.

*Casos de uso*

*Atores*

• 	Usuário: Aluno/Professor que consulta catálogo, reserva, empresta, renova e devolve.
• 	Bibliotecário: Administra catálogo, usuários, regras, autoriza operações e gera relatórios.
• 	Sistema de notificação: Serviço interno que dispara lembretes (simulado).

*Diagrama de casos de uso (descrição textual)*

• 	Usuário
• 	UC1: Buscar livros
• 	UC2: Visualizar detalhes de livro
• 	UC3: Reservar livro
• 	UC4: Solicitar empréstimo
• 	UC5: Renovar empréstimo
• 	UC6: Devolver livro
• 	UC7: Consultar histórico e multas
• 	Bibliotecário
• 	UC8: Cadastrar/editar livro
• 	UC9: Cadastrar/editar usuário
• 	UC10: Registrar empréstimo/devolução
• 	UC11: Configurar políticas (prazos, multas)
• 	UC12: Gerar relatórios
• 	Sistema de notificação
• 	UC13: Notificar vencimento
• 	UC14: Notificar disponibilidade de reserva
• 	UC15: Notificar multa aplicada

*Especificações resumidas*

• 	UC1 — Buscar livros: Entrada por texto/filtros; saída lista de resultados; regra: busca parcial por título/autor/assunto/ISBN.
• 	UC4 — Solicitar empréstimo: Pré-condição: usuário ativo, sem bloqueio; pós-condição: empréstimo criado e exemplar decrementado; exceções: sem disponibilidade, multas pendentes.
• 	UC5 — Renovar: Pré: empréstimo ativo; regra: sem reservas pendentes; limite de renovações; saída: nova data de devolução.
• 	UC3 — Reservar: Pré: sem exemplares disponíveis; saída: posição na fila; regra: expiração de reserva quando exemplar disponível e não atendido em X horas.

*Diagramas de atividades*

*Fluxo de empréstimo*

• 	Início: Usuário solicita empréstimo.
• 	Verificações:
• 	Perfil e status: usuário ativo? sem bloqueios/multas?
• 	Disponibilidade: há exemplares livres?
• 	Decisão:
• 	Se não disponível: oferecer reserva; fim.
• 	Se disponível: registrar empréstimo.
• 	Processo: calcular data de devolução conforme política; decrementar estoque; registrar log.
• 	Saída: confirmação e notificação.

*Fluxo de devolução*

• 	Início: Usuário devolve exemplar.
• 	Processo: localizar empréstimo, calcular atraso, aplicar multa se necessário.
• 	Atualização: incrementar estoque; fechar empréstimo; atualizar status de multa.
• 	Gatilho: se há reserva pendente, notificar próximo da fila.
• 	Saída: recibo e estado atualizado.

*Fluxo de reserva*

• 	Início: Usuário solicita reserva de livro indisponível.
• 	Processo: inserir na fila do livro com timestamp.
• 	Gatilho: quando exemplar retorna, notificar primeiro da fila e abrir janela de retirada.
• 	Decisão: se reserva expira, avançar para próximo da fila.
• 	Saída: confirmação de posição e prazo.

*Modelo entidade-relacionamento*

*Entidades e atributos*

• 	Usuário (Usuario)
• 	idUsuario: identificador
• 	nome: texto
• 	email: texto único
• 	perfil: aluno  professor  bibliotecario
• 	status: ativo  bloqueado
• 	multasPendentes: valor
• 	Livro (Livro)
• 	idLivro: identificador
• 	titulo: texto
• 	autor: texto
• 	assunto: texto
• 	isbn: texto único
• 	exemplaresTotais: inteiro
• 	exemplaresDisponiveis: inteiro
• 	ativo: booleano
• 	Empréstimo (Emprestimo)
• 	idEmprestimo: identificador
• 	idUsuario: FK
• 	idLivro: FK
• 	dataEmprestimo: data
• 	dataPrevistaDevolucao: data
• 	dataDevolucao: data opcional
• 	renovacoes: inteiro
• 	multaAplicada: valor
• 	Reserva (Reserva)
• 	idReserva: identificador
• 	idUsuario: FK
• 	idLivro: FK
• 	dataReserva: data
• 	status: ativa  atendida  expirada
• 	posicaoFila: inteiro
• 	Política (Politica)
• 	idPolitica: identificador
• 	prazoDias: inteiro
• 	multaPorDia: valor
• 	limiteRenovacoes: inteiro

*Relacionamentos e cardinalidades*

• 	Usuario 1..N Emprestimo: um usuário pode ter muitos empréstimos; cada empréstimo pertence a um usuário.
• 	Livro 1..N Emprestimo: um livro pode estar em muitos empréstimos ao longo do tempo.
• 	Usuario 1..N Reserva: um usuário pode ter muitas reservas.
• 	Livro 1..N Reserva: um livro pode ter muitas reservas (fila).
• 	Politica 1..N Emprestimo: política vigente aplicada no momento do empréstimo (pode ser embutida por valor ou referenciada).
• 	Restrições: exemplaresDisponiveis >= 0; empréstimo ativo se dataDevolucao nula; reserva válida se livro indisponível.

Protótipo de telas (mid-fidelity)
Abaixo um protótipo simples com HTML+CSS. Salve como arquivos separados e abra em navegador. Páginas: login.html, dashboard.html, catalogo.html, detalhes.html, emprestimo.html, reservas.html, usuarios.html, estilos.css.
