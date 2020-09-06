# testes-cursos
Testes de comportamentos automatizados

## Para garantir que um método seja invocado apenas uma única vez num mock, devemos passar um segundo argumento para o método verify, que é o times(1):
`verify(daoFalso, times(1)).atualiza(leilao1);` verify metodos executados uma quantidade de vezes.

## Repare que o método times recebe como argumento o número de vezes que o método deve ser invocado. Ou seja, você pode verificar que um método foi chamado exatamente 4 vezes, se for necessário.

    `@Test
    public void naoDeveEncerrarLeiloesQueComecaramMenosDeUmaSemanaAtras() {
        RepositorioDeLeiloes daoFalso = mock(LeilaoDao.class);
        when(daoFalso.correntes()).thenReturn(Arrays.asList(leilao1, leilao2));
        EncerradorDeLeilao encerrador = new EncerradorDeLeilao(daoFalso);
        encerrador.encerra();
        assertEquals(0, encerrador.getTotalEncerrados());
        assertFalse(leilao1.isEncerrado());
        assertFalse(leilao2.isEncerrado());
        verify(daoFalso, never()).atualiza(leilao1);//exemplos de verify de metodos nunca executados.
        verify(daoFalso, never()).atualiza(leilao2);
    }`
    
## Ainda podemos passar atLeastOnce(), atLeast(numero) e atMost(numero) para o verify().

O nome do método nos ajuda a inferir o que ele faz. Discuta para que serve cada um destes métodos auxiliares.

VER OPINIÃO DO INSTRUTOR
Opinião do instrutor

O método atLeastOnce() vai garantir que o método foi invocado no mínimo uma vez. Ou seja, se ele foi invocado 1, 2, 3 ou mais vezes, o teste passa. Se ele não for invocado, o teste vai falhar.

O método atLeast(numero) funciona de forma análoga ao método acima, com a diferença de que passamos para ele o número mínimo de invocações que um método deve ter.

Por fim, o método atMost(numero) nos garante que um método foi executado até no máximo N vezes. Ou seja, se o método tiver mais invocações do que o que foi passado para o atMost, o teste falha.

Veja que existem diversas maneiras diferentes para garantir a quantidade de invocações de um método! Você pode escolher a melhor e mais elegante para seu teste!


## Garantindo que os métodos foram executados na ordem certa
PRÓXIMA ATIVIDADE

Teste agora que o leilão é realmente enviado por e-mail. Esse teste é análogo ao teste que você fez para garantir que o leilão é persistido no DAO.

Só que, dessa vez, garanta que os métodos foram executados nessa ordem específica: primeiro o DAO, depois o envio do e-mail.

Para isso, faça uso do método inOrder() do Mockito. Veja um exemplo de utilização:

        // passamos os mocks que serao verificados
        InOrder inOrder = inOrder(daoFalso, carteiroFalso);
        // a primeira invocação
        inOrder.verify(daoFalso, times(1)).atualiza(leilao1);    
        // a segunda invocação
        inOrder.verify(carteiroFalso, times(1)).envia(leilao1);    
por exemplo

    `@Test
    public void deveEnviarEmailAposPersistirLeilaoEncerrado() {
        Calendar antiga = Calendar.getInstance();
        antiga.set(1999, 1, 20);

        Leilao leilao1 = new CriadorDeLeilao().para("TV de plasma")
            .naData(antiga).constroi();

        RepositorioDeLeiloes daoFalso = mock(RepositorioDeLeiloes.class);
        when(daoFalso.correntes()).thenReturn(Arrays.asList(leilao1));

        EnviadorDeEmail carteiroFalso = mock(EnviadorDeEmail.class);
        EncerradorDeLeilao encerrador = 
            new EncerradorDeLeilao(daoFalso, carteiroFalso);

        encerrador.encerra();

        InOrder inOrder = inOrder(daoFalso, carteiroFalso);
        inOrder.verify(daoFalso, times(1)).atualiza(leilao1);    
        inOrder.verify(carteiroFalso, times(1)).envia(leilao1);    
    }`
    
 ## Como mockar uma exceção?
    
    `@Test
    public void deveDesistirSeDaoFalhaPraSempre() {
        Calendar antiga = Calendar.getInstance();
        antiga.set(1999, 1, 20);

        Leilao leilao1 = new CriadorDeLeilao().para("TV de plasma")
            .naData(antiga).constroi();
        Leilao leilao2 = new CriadorDeLeilao().para("Geladeira")
            .naData(antiga).constroi();

        RepositorioDeLeiloes daoFalso = mock(RepositorioDeLeiloes.class);
        when(daoFalso.correntes()).thenReturn(Arrays.asList(leilao1, leilao2));

        EnviadorDeEmail carteiroFalso = mock(EnviadorDeEmail.class);
        doThrow(new RuntimeException()).when(daoFalso).atualiza(leilao1);
        doThrow(new RuntimeException()).when(daoFalso).atualiza(leilao2);

        EncerradorDeLeilao encerrador = 
            new EncerradorDeLeilao(daoFalso, carteiroFalso);

        encerrador.encerra();

        verify(carteiroFalso, never()).envia(leilao1);
        verify(carteiroFalso, never()).envia(leilao2);
    }`
## Argumentos Genericos:


PRÓXIMA ATIVIDADE

No exercício anterior usamos o método any() da classe org.mockito.ArgumentMatchers para dizer ao Mockito que nosso mock object poderia receber qualquer objeto do tipo Leilao.

A classe ArgumentMatchers tem muitos outros métodos. Veja a lista de métodos dela. Para que servem esses métodos?

A classe Matchers possui diversos métodos que podem ser usados para especificarmos que argumentos nosso mock pode receber numa chamada de método. Isso permite escrever testes mais facilmente e deixa nosso código mais flexível.

Podemos, por exemplo, garantir que um mock vai ser chamado com uma String começando com "Importante:". Veja só:

`verify(meuMock).imprimeMensagem(startsWith("Importante:"));`
