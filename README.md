# Divine
<script>
  /*
  *Tag responsável por enviar a camada de dados para o penguin-datalayer-collect
  */
  analyticsHelper.safeFn('Penguin Datalayer Collect ', function(helper){
    // Array do dataLyer, filtrando os eventos nativos do GTM e easyCollect
    var body = window.dataLayer.filter(function(item) {return /gtm\.+|ga_pageview|midia_pageview/.test(item.event) == false});

    if (habilitarAmostragemValidacao() === 'true') {
      var request = new XMLHttpRequest();
      //Os dados de validação podem ser enriquecidos com dados de negocios enviados como queryString
      request.open("POST", {{endpoint - penguin-datalayer - collect}} + "?schema="+ {{schema}} , true);
      request.setRequestHeader('Content-Type', 'application/json');
      request.onreadystatechange = logHttpResponse;
      request.send(JSON.stringify(body));
    }

    function habilitarAmostragemValidacao() {
      function random(min, max) {
        min = Math.ceil(min);
        max = Math.floor(max);
        return Math.floor(Math.random() * (max - min)) + min;
      }

      var sample = 1;
      var domain = {{Cookie - Domínio}} ? {{Cookie - Domínio}} : 'auto';
      var cookie_penguin_datalayer_collect = helper.cookie('penguin_datalayer_collect');

      /* Limitador realizar o envio apenas de uma amostragem dos usuários, assim é possível reduzir
      os custos de GCP, não deixando a tag ativas para todos os usuários.*/
      if (!cookie_penguin_datalayer_collect) {
        cookie_penguin_datalayer_collect = (random(0, 100) <= sample) ? 'true' : 'false';
        helper.cookie('penguin_datalayer_collect', cookie_penguin_datalayer_collect, {'exdays': 1, 'domain': domain});
      }

      return cookie_penguin_datalayer_collect;
    }

    function logHttpResponse() {
      if ({{Debug Mode}}) {
        console.log('Penguin-datalayer-collect - Status: ', this.status);
        console.log('Penguin-datalayer-collect - Object dataLayer:', window.dataLayer);
        console.log(JSON.stringify(window.dataLayer));
      }
    }
  });
</script>
