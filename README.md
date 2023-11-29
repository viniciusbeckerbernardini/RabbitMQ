# RabbitMQ:
- Message Broker;
	- É o Douglas, o ultimo 10, meio de campo;
- Implementa AMQP, MQTT, STOPM e HTTP;
- Desenvolvimento em Erlang;
- Desacoplamento entre serviços;
- Rápido e poderoso:
	- Mensagens caem na memória o que faz o processo seja extremamente rápido.
- Padrão de mercado;

## Por baixo dos panos:
- Client -> Server
	- TCP:
		- Primeira conexão lenta em detrimento de handshake
- RBMQ abre uma única conexão com o servidor facilitando e agilizando a conexão;
	- Multiplexing conncetion due to channels;
	- Sempre que abro um channel é uma nova thread;

- Rabbit:
	- Publisher -> Exchange -> Queue -> Consumer;

- Tipos de exchange:
	- Direct -> Manda especificamente para uma determinada fila;
	- Fannout -> Manda para todas as filas que estão relacionadas com essa exchange;
	- Topic -> Exchange com regras que encaminha de acordo com a regra do routing key;
	- Header -> No header da mensagem é determinado qual sua fila de destino;

	- Direct Exchange:
	```
																	 -> Queue -> Consumer
																	/ bind (routing key x)
		 Producer (routing key) -> Exchange - bind (routing key y) -> Queue -> Consumer
																	\ bind (routing key z)
																	 -> Queue -> Consumer
	```
	- bind: relação de fila com exchange
	- routing key: chave especifica que relaona fila com exchange;

	- Fannout:
		```
								 -> Queue -> Consumer
								/ 
		  Producer -> Exchange -> Queue -> Consumer
								\ 
								 -> Queue -> Consumer
		```

	- Não passamos routing key;
	- Mensagem replicada para todas filas;
	- Usado para sistemas que usam a mesma informação como por exemplo um sistema de vendas que quando há uma compra a mensagem é recebida por logs, marketing, venda, etc.

	- Topic:
		- Topic:
		```
																		 -> Queue -> Consumer
																	/ bind (routing key x)
		- Producer (routing key) -> Exchange - bind (routing key y) -> Queue -> Consumer
																	\ bind (routing key z)
																	 -> Queue -> Consumer
		```
		- Semelhante a direct porém usa um like pra fazer o match dos routing key

- Queues: 
	- ED FIFO;
	- Propriedades:
		- Durable: se ela vai perssisitr no disco depois do restart do broker (padrão true);
		- Auto-delete: Removida automaticamente quando o consumer se desconecta;
		- Expiry: Define o tempo que não há mensagem ou clients consumindo;
		- Message TTL: Tempo de vida da mensagem, se ninguém consumir em X tempo ela vai sumir;
		- Overflow:
			- Drop head (remove a última);
			- Reject public (quando a fila está lotada o publicador não consegue mais publicar na fila).
		- Exclusive: Somente channel que criou pode acessar;
		- Max length ou bytes: Quantidade de mensages ou tamanho em bytes máximo permitido.
			- Caso aconteça daí fazemos as ações do overflow;
- Dead letter queues:
	- Algumas mensagens não conseguem ser entregues por qualquer motivo;
	- São encaminhadas para uma exchange especifica que roteia as mensagens para uma dead letter queue;
	- São consumidar por uma aplicação que fica observando apenas essa fila;

- Lazy Queues:
	- Mensagens são armazenadas em disco:
		- Usada quando os consumers não são capazes de consumir as mensagens pelo volume;
	- Exige alto I/O
		- Leitura em disco é lenta em relação a memória RAM;
	


## Confiabilidade (reliability):
- Consumer Ackkowledgment;
	- Basic.Ack = Consumer da um OK, conseguiu processar a mensagem;
	- Basic.Reject = Consumer reenvia a mensagem em detrimento de um erro;
	- Basic.Nack = Como o Reject mas rejeita várias mensagens ao mesmo tempo
- Publisher Confirms;
	- Mensagem possui um id;
	- Atribuido pelo publisher;
	- A exchange retorna um ack informando que a mensagem foi recebida;
	- Caso a exchange tenha algum problema interno ela retorna um nack informando que a mensagem deu ruim gurizada bah daí é fogo né cupin
- Filas e mensagens duravéis/persistidas;
