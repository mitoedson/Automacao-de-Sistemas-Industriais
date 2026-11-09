# Experimento 13 — Carregamento de vagões 

![alt text](image-15.png)

Um sistema para carregamento de vagões é composto pelos seguintes elementos: uma esteira acionada pelo motor M, uma eletroválvula Y1 para permitir saída de produtos do silo, um sensor S3 para detectar a presença de um vagão, um sensor balança B1 para indicar que o vagão está cheio e uma trava de vagão Y2, cujo destravamento é feito por eletroímã. A partir de um comando de partida (PTD), o sistema estará pronto para funcionar.

Com a chegada do primeiro vagão (indicado por S3), é acionado o motor da esteira, sendo que só será desligado após o último vagão ser carregado. A identificação do último vagão é feita por uma supervisão de tempo (15s) contado após a saída do vagão previamente carregado. Com o correto posicionamento do vagão e não estando cheio, tem início o seu enchimento dado pela abertura de Y1.

O travamento dos vagões na posição correta é feito por um atuador mecânico pela força de uma mola, e o seu destravamento exige a atuação elétrica do eletroímã Y2. Após o enchimento do vagão, fecha-se a eletroválvula e aguardam-se 7 segundos para o esvaziamento da esteira. A partir deste instante, o vagão é destravado. Crie uma lista de entradas e saídas para esse processo e faça a programação em ladder. 