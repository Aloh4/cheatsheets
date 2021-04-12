##1 - Conceitos DOCSIS

	DOCSIS  - Data Over Cable Service Interface Specification
		- Padrão aberto desenvolvido pelo cable labs afim de baratear o serviço de banda larga em redes HFC.
		- Este padrão determina o formato de transmissão no meio físico


	Provisionamento - Adaptação/Implantação de um serviço em redes de telecomunicações 
			  Exe: A operadora provisiona (instala e configura) circuitos para conectar uma banda de dados para os clientes.
	
	RX/TX	- São abreviações de Receive and Transmit
		  Exe: O cliente 'envia' (upload) uma frequencia ao CMTS -> Transmit  (Upstream)
		  Exe: O cliente 'recebe'(download) uma frequencia do CMTS -> Receive (Downstream)

	O Rx/Tx do CableModem é de 43db / do CMTS é 0dB
	Nunca deve haver Tx maior que 52dB
---------------------

##Processo Sync do Cabe Modem

	CM envia DHCP Discover ao CMTS
	CMTS envia a requisição ao servidor DHCP, que verifica no LDAP a existência do equipamento
	Após a verificação do LDAP, o servidor DHCP envia um OFFER ao CMTS com os parâmetros a serem entregues ao CM
	O CM envia um DHCP Request ao CMTS que envia ao Servidor DHCP
	O servidor DHCP envia um ACK ao CMTS que por fim, envia ao CM


	No Docsis 1.0, todos os serviços competem por uma banda upstream baseando-se no Best Effort
	No Docsis 1.1, cada serviço pode ter garantia de qualidade baseados no QoS (largura de banda, jitter)
----------------------

##BSod -	Implementa o DOCSIS L2VPN (Layer-2 VPN) que consiste na entrega de uma VLAN transparente do roteador da operadora até o CM (L2 Transparent Lan Service) (TLS)
Business Services over DOCSIS


##Tecnologias usadas:


	nRTPS - Non Real Time Polling Service - Um dos tipos de QoS que podem ser configurados no CM
		O principal ganho é a garantia da oportunidade de transmissão, mesmo com a rede congestionada
		
	Garantia de Banda - clientes BSoD terão por padrão, garantia de banda para transmissão de seus pacotes
			  - a banda reservada deve ser igual a banda configurada
			  - Uma vez habilitado o Admission Control, o CMTS garantirá que quando a quantidade de banda reservada chegue perto dos 100%,
			    outros CM's não irão sincronizar.

	Cable Modem Steering - Dividi os serviços e canais, dentro de um mesmo MAC Domain
			     - Utiliza um grupo específico (TLV) para separar os CM entre canais sem a 'separação física'
			     - Abre mão do "attribute-mask' e aloca US e DS de acordo com os critérios desejados.
			
	Exemplo:
		CM sincroniza o DS já trazendo o 'attribute-mask' = 0x01,
		Se o canal sincronizado estiver com 'attribute mask' diferente...
		O CMTS deverá procurar um canal idêntico ao configurado no CM
		Ao indentificar esse canal, o CM é automaticamente incluído.

		CM possui attribute-mask = 0x02 = Virtua
		CM possui attribute-mask = 0x01 = BSoD

##Importante:

	O tráfego dos clientes BSoD, trafegarão por links Embratel exclusivos, não competindo com clientes Virtua
	Todo o tráfego BSoD, será transportado utilizando QinQ dentro da rede NET, utilizando apenas uma VLAN de transporte
	Modems BSoD coexistirão juntos aos clientes Virtua, porém o tráfego gerado por clientes não passará de 2Mbps (UP e DOWN)
	Será utilizado uma interface Giga exclusiva para transportar dados dos clientes BSoD
	Não se pode dentro do CMTS, haver 2 VLAN ID's iguais, caso contrário, apenas 1 será sincronizado.
	O VLAN ID será sincronizado no LDAP 

--

	Loopbacks/Bundle/Cable-mac = Vai o endereço default de cada rede, sem o loopback, os cables não tem referencia de classe!
		    	   Cada loopback, deve ter uma classe de IP primária, e caso exista serviços por IP, deve haver uma classe IP secundária


	Rota default = Configurada para quando o CMTS possa encaminhar um pacote quando ele não conhecer o seu destino

	LoadBalance = Serve para fazer balanceamento de carga entre os canais que possuem o mesmo grupo associado,
		      O balanceamento é feito com a regra e policy associadas !

	A Rule do load balance é a regra que o CMTS deve seguir para fazer o balanceamento
	A Policy é a parte da configuracao onde associamos as regras para que o CMTS possa executá-las

	Subinterfaces = Indicam as loopback's configuradas no CMTS
	Placa Giga = Onde é feito a comunicação do CMTS com o Router(entrada/saída de tráfego)

	PMD - arquivo contendo informações sobre uma falha que ocorreu, ele é analisado pelo Fabricante
	    - Sempre deve ser feito a coleta do PMD, e após deletá-lo.
	

	* Wideband-Cable // Integrated-Cable
		
		1 - Especifica o tamanho da banda reservada para os cables na interface, e refere-se somente a banda dos cables DOCSIS 3.0
		2 - Tem a mesma função da Wideband-Cable, porém para outras versões do DOCSIS (1.x, 2.0)

----------------------
##CMTS - > Cable Modem Termination System

	Conjunto de dispositivos com funções específicas para gerar, processar, transmitir e receber dados e gerenciar sua transmissão através da rede DOCSIS
	
	Loopbacks utilizadas:

		10 - Clientes Virtua
		30 - HDTV
		80 - PME
		100 - Sem cadastro

	LoadBalance = Distribui igualmente o tráfego de voz/dados entre as US e DS pertencentes ao mesmo grupo.
		      Quando configurado, os cables são movidos do canal atual para o canal com menor utilização.
		    * Pode-se utilizar o balanceamento por quantidade de cables/ou utilização de tráfego

	* Etapas do Cable Modem

		* O CM varre o canal de DOWNSTREAM a procura de uma frequencia DOWNSTREAM
			1 - Busca/varre o espectro de frequencia(RF) em busca de uma DS
			Etapas:	1 - CM busca modulação (64/256QM)
				2 - CM procura Forward Error Correction (FEC) frames codificados
				3 - CM procura por pacotes (MPEG) dentro destes frames
				4 - CM procura msgs DOCSIS SYNC dentro dos pacotes MPEG
				    4.1 - As mensagens SYNC definem são para sincronizar o calendário DOCSIS entre CMTS e CM (Define o momento que cada CM pode falar) 
				OBS: Annex B - indica que é um canal DS de 6MHz Docsis.
					
		* Obtém a frequencia//parâmetros de UPSTREAM do CMTS (UCD - Upstream Channel Descriptor - Modulação, tamanho do canal, frequencia da US)
			1 - O modem encontra o US recolhendo MSGs UCD, colocadas no canal de DS pelo CMTS
			2 - As MSGs UCD contem as características de transmissão de cada canal US (canal US, frequencia US, largura de canal US)
			3 - Nos pacotes UCD, estão os escritores BURST que definem como o CM envia diferentes tipos de pacotes DOCSIS
			
			    OBS: Em um CMTS CISCO, os bursts são definidos em uma Modulation Profile
			    OBS: 1 canal de DOWNSTREAM controla múltiplos canais de UPSTREAM
				 O CM coleta as UCDS de todas as UPSTREAM associadas ao DOWNSTREAM
				 Em seguida, ele determina os quais é compatível e seleciona 1 UPSTREAM para a transmissão
				 Se nenhum canal UPSTREAM é aceitável, o CM volta para etapa anterior e procura um novo canal DOWNSTREAM
		
		* Assim que o CM é selecionado, ele começa o processo de RANGING
			1 - O CMTS envia mensagens MAP (Bandwith Allocation MAPS) para descrever os dispo. autorizados a transmitir dados e em qual intervalo de tempo
			2 - Cada intervalo de tempo é associado a um SID para identificar os dipositivos autorizados a utilizar o intervalo
			3 - O CM usa um desses intervalos para transmitir um Ranging Request(RNG-REQ) usando a inicialização do SID (utiliza 0 para inicialização)
			4 - O CM continua a transmitir nesse intervalo até receber confirmação do CMTS ou esgotar as tentativas
			5 - O CM transmiti em algum nível de potência específica, e se não receber notificação, aumenta a potência até seu máximo
			6 - O CM espera um RNG-RESPONSE do CMTS // O CMTS envia essa mensagem após decifrar o RNG-REQ enviada pelo CM
			7 - Ou seja, o CM está transmitindo em um nível de potência que o CMTS consiga detectar
			8 - As msgs RNG-RSP tambem são usadas para ajustar os parâmetros de transmissão do CM = Ex. Frequência
		
		* Estabelecer conectividade IP
			1 - O CM faz um pedido de banda para enviar o pacote DHCP (DHCP REQUEST) ao CMTS
			2 - Se o CMTS poder responder, ele envia uma msg MAP ao CM em seguinda
			3 - O CM envia um DHCP DISCOVER (init.d)(para descobrir os servidores DHCP)
			4 - Essa requisição é enviada aos servidores LDAP para verificar o equipamento (LDAP Search // Dados CM)
			5 - Um DHCP Offer é enviado e o modem entra no estado init.io
			6 - O CM seleciona um dos DHCP Offer e emite um DHCP REQUEST
			7 - O Servidor DHCP envia um DHCP ACK, confirmando a configuração de parametros do CM.

		* Time of Day - Etapa 1
			1 - O CM obtem hora/data atual utilizando o protocolo TOD
			2 - Lembrar = O DHCP #4 define o servidor de tempo (ToD) e o DHCP #2 define o time offset
			3 - O CM envia um ToD request ao servidor
			4 - O CM passa para o estado init(t) do CMTS
			5 - O ToD responde com data e hora atual por um ToD Response
			Obs: O arquivo de configuração é dividido em partes e enviado individualmente por TFTP
		
		* Transferência de Informações Etapa 1.1
			1 - O arquivo de configuração DOCSIS é transferido para o CM via TFTP
			2 - O CM inicia uma solicitação TFTP para o seu arquivo de configuração
			3 - O endereço IP do server TFTP e o nome do arquivo são especificados nas mensagens DHCP
			4 - O servidor TFTP localiza o arquivo e inicia uma transferência de dados em pacotes TFTP
			5 - Quando o CMTS detecta que o CM está transferindo o seu arquivo de configuração, o CM passa para o estado init(o)

		* Registro
			1 - O CM após ter recebido seu arquivo de configuração, verifica a validade do arquivo
			2 - Ele também certifica-se de que todos os parâmetros obrigatórios do arquivo estão presentes
			3 - E ainda, executa uma validação de integridade da mensagem (Message integrity check(MIC)) 
			4 - Caso uma dessas etapas falhar, o modem permanece no estado init(o)
			5 - Se a verificação for positiva, o CM inicia as seguintes etapas:

			1.1 - O CM utiliza o arq. de configuração para criar uma mensagem DOCSIS(REG-REQ) e enviá-la ao CMTS
			1.2 - Após receber a msg, o CMTS realiza a verificação da integridade da mensagem
			1.3 - Para isso, o CMTS tem um shared-secret para calcular o valor da mensagem (hash?)
			1.4 - OBS: O shared secret também é conhecido pelo sist. de aprovisionamento, porém não pelo CM e portanto, o CMTS pode verificar se 
			 	   a mensagem foi alterada ao longo do caminho
			1.5 - OBS: O sistema de aprovisionamento calcula o CMTS MIC e inclui no arq. de configuração para ser passado de forma transparente 
			      	   pelo modem ao CMTS
			1.6 - O CMTS usa a shared secret configurada + parametros do REG-REQ para calcular o CMTS MIC (msg DOCSIS enviada CM -> CMTS)
			1.7 - Se o REG-REQ foi alterado, uma alteração ocorre no MIC, e o REG-REQ é rejeitado
			1.8 - Se for rejeitado, o CM passa para o estado reject(m)
			1.9 - Se for aceitado, uma resposta REG-RSP é enviada ao CM
			1.10 - O CM confirma o recebimento do REG-RSP enviando um REG-ACK para o CMTS
			1.11 - Neste momento, o CM está em estado online no CMTS.		
			
		* Inicialização Baseline Privacy (BPI)
			1 - Uma chave de autorização é negociada entre o CM e o CMTS
			2 - A partir desta chave de autorização, as chaves de criptografia de tráfego são geradas
			3 - Essas chaves são usadas para criptografar o tráfego de dados
			4 - Se tudo ocorrer bem, o CM fica online, e entre no estado init(pt).

			Detalhes:
			1.1 - O BPI+ fornece criptografia de dados através de redes DOCSIS
			1.2 - Também utiliza certificados digitais para autenticar os CM com o CMTS
			1.3 - Os CM tem um cert. digital X.509 da UIT, que é assinado pelo fabricante
			1.4 - Este fabricante, é autenticado pela autoridade raiz DOCSIS
			1.5 - Há cinco mensagens BPKM, e a primeira é opcional (BPKM-REQ-Authorization Information)
			1.6 - Essa msg é utilizada pelo CM para enviar o certificado X.509 do fabricante do CM ao CMTS
			      Obs: Se essa msg não for utilizada, o CMTS usará outros meios para aprender o certificado do CM
			
			Continuação:
			1.1 - O CM envia o BPKM-REQ-AUTHORIZATION-REQUEST contendo seu certificado,public key e informações como MAC, ident do fabricante, nº série
			      Obs: Também é incluído na msg recursos de segurança e o SID primário			
			1.2 - O CMTS valida o cert. do CM e usa a public key para criptografar a authorization key
			1.3 - A chave de autorização é enviada ao CM pelo BPKM-RSP-AUTHORIZATION-REPLY
			1.4 - O CM usa essa chave para iniciar uma negociação segura das traffic encryption keys (TEKs)
			1.5 - O pedido por TEKs é enviado por BPKM-REQ-KEY-REQUEST e são retornadas pelo CMTS por BPKM-RSP-KEY-REPLY
			      Obs: Tanto as TEKs quanto a AUTHORIZATION KEY tem vidas limitadase precisam ser regeneradas periodicamente !			
			1.6 - Assim que todas as chaves são negociadas, o modem entra em estado online init(pt) 
						

		Estados INIT do Cable Modem
			* init(r1), init(r2) = Ranging 
			* init(rc) = CM recebeu DOCSIS-RNG-RESP (Upstream disponível)wha
			             Obs: Se o CM não consegue sair do estado init(rc), o canal de UPSTREAM pode estar saturado  
			* init(d)  = CM envia ao CMTS o DHCP DISCOVER
			* init(io) = CM recebe DHCP OFFER do CMTS
			* init(dr) = CM envia ao CMTS o DHCP REQUEST
			* init(i)  = CM recebe do CMTS DHCP ACK
			* init(t)  = CM envia um ToD-REQUEST ao servidor Time of Day (solicita data/hora)
			* init(o)  = CM envia TFTP-READ-REQUEST (CMTS detecta que o CM está transferindo seu arq. de configuração)
				     Obs: Na etapa de registro, o CM verifica se todos os itens necessários estão presentes no arquivo de config.
					  Caso algum parâmetro esteja faltando, o CM permanece no estado init(o), e não prossegue para os demais. 
			* online    = Etapas:
				     1 - Modem valida o arq de configuração
				     2 - Envia o Registration Request(REG-REQ) ao CMTS
				     3 - CMTS valida a mensagem e verifica se pode responder ao pedido
				     4 - Se poder, atribui identificação ao CM com um Registration Response(REG-RSP)
				     5 - CM reconhece a mensagem, se estiver tudo OK, CM passa para o estado ONLINE 	
			* reject(m) = Falha na verificação de integridade do REG-REQ pelo CMTS (alteração, violação)	
			* online(pk) = CM recebe o BPKM-RSP-AUTHORIZATION-REPLY
			* online(pt) = Finaizou com sucesso o processo de troca de chaves BPI+ (CM recebeu BPKM-RSP-KEY-REPLY)
