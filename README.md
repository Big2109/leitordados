# leitordados

//Algumas partes do código foram retiradas por questao de segurança.


namespace CNFBrasil
{
    class Program
    {
        public static CookieContainer _cookies = new CookieContainer();
        static string urlBase = "https://www.*qualquersite*.com.br/";

        public static void Main(string[] args)
        {        
            //Entrada de dados no site
            dtDados = importacao.ObterDadosCsv($@"{Environment.CurrentDirectory}\ENTRADA.csv");      

            Console.WriteLine("Começando o loop");
            int linhaProcs = 0, linhaErro = 0;

            string htmlResult = "";
            string nome = string.Empty;

            //Lista caso queira usar
            List<string> uf = new List<string> { "AC", "AL", "AP", "AM", "BA", "CE", "DF", "ES", "GO", "MA", "MT", "MS", "MG", "PA", "PB", "PR", "PE", "PI", "RJ", "RN", "RS", "RO", "RR", "SC", "SP", "SE", "TO" };

            string urlProxy = "";
            Uri proxyServer = null;

            var list = dtDados.Rows.ToDynamicList<DataRow>().Where(r => r[1].AsBool() != true).ToList();

            foreach (DataRow row in list)
            {
                try
                {
                  
                    {
                        //Loop na sua lista
                        foreach (string uf_select in uf)
                        {
                            int pagina = 1;
                            nome = row["NOME"].ToString();
                            string test = row["checar"].ToString();


                            pagina:
                            string url = $"https://www.*seusite*.com.br";

                            try
                            {

                                HttpWebRequest request2 = (HttpWebRequest)WebRequest.Create(url);

                                request2.Proxy = new WebProxy(proxyServer);
                                request2.CookieContainer = _cookies;
                                request2.Accept = "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7";
                                request2.Headers.Add(HttpRequestHeader.AcceptEncoding, "gzip, deflate, br");
                                request2.Headers.Add(HttpRequestHeader.AcceptLanguage, "en-US,en;q=0.9,pt;q=0.8");
                                request2.Method = "GET";
                                request2.Host = "www.falecidosnobrasil.org.br";
                                request2.UserAgent = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/117.0.0.0 Safari/537.36";
                                HttpWebResponse response2 = (HttpWebResponse)request2.GetResponse();
                                //System.Threading.Thread.Sleep(20000);
                                using (StreamReader sr2 = new StreamReader(response2.GetResponseStream(), Encoding.UTF8))
                                {
                                    htmlResult = sr2.ReadToEnd();
                                }
                                //escrever os resultados
                                string resultado1 = "";
                                string resultado2 = "";


                                HtmlAgilityPack.HtmlDocument doc = new HtmlAgilityPack.HtmlDocument();

                                doc.LoadHtml(htmlResult);

                                var check = doc.DocumentNode.SelectSingleNode("//div[@class='card text-start w-98 ']");
                                if (check != null)
                                {
                                    //MAXIMO DE PAGINAS
                                    var maxpagina = doc.DocumentNode.SelectSingleNode("//div[@style='display: flex; justify-content: center; align-items: center']").InnerText.Replace("\t\n ", "").Replace($"\n\t\n\tPágina {pagina}\t\n\tde ", "").Trim();

                                    var div = doc.DocumentNode.SelectSingleNode("//div[@class='table-responsive']");
                                    var falecidos = div.SelectNodes("//div[@class='card text-start w-98 ']");

                                    foreach (var node in falecidos)
                                    {

                                        //resultado1
                                        resultado1 = node.ChildNodes[1].SelectSingleNode("h5").InnerText;

                                        //resultado2
                                        resultado1 = node.ChildNodes[1].SelectSingleNode("h6").InnerText


                                        string result = string.Concat(nome_obito + ";", cidade + ";", estado + ";", nome_mae + ";", dt_obito + ";", nome + ";", pagina);

                                        Exportacao.Escreve($@"{Environment.CurrentDirectory}\SAIDA.csv", result);
                                    }


                                    while (pagina < maxpagina.AsInt())
                                    {
                                        Console.WriteLine($"NOME: {nome} - UF: {uf_select}. {pagina} de {maxpagina} Terminadas");
                                        pagina = pagina + 1;
                                        goto pagina;
                                    }
                                    Console.WriteLine($"NOME: {nome} - UF: {uf_select}. {pagina} de {maxpagina} Terminadas");
                                }
                            }
                            catch (Exception ex)
                            {
                                linhaErro++;
                                Exportacao.Escreve($@"{Environment.CurrentDirectory}\ERRO.csv", nome + ";" + uf_select);
                            }
                        }
                        
                    }
                    row["checar"] = true;
                }
                catch (Exception response2)
                {
                    linhaErro++;
                    Exportacao.Escreve($@"{Environment.CurrentDirectory}\ERRO_PROXY.csv", proxyServer + ";");
                }
            }            
        }
        //Método GET
        {
            string htmlResult = "";

      
            byte[] byteArray = Encoding.UTF8.GetBytes(parametros);

            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(urlCaptcha);

            request.Proxy = new WebProxy(proxyServer);
            request.Accept = "*/*";
            request.CookieContainer = _cookies;
            request.Headers.Add(HttpRequestHeader.AcceptEncoding, "gzip, deflate, br");
            request.Headers.Add(HttpRequestHeader.AcceptLanguage, "en-US,en;q=0.9,pt;q=0.8");
            request.Headers.Add("Origin", "https://www.falecidosnobrasil.org.br");
            request.ContentType = "application/x-www-form-urlencoded; charset=UTF-8";
            request.Method = "POST";
            request.Host = "www.falecidosnobrasil.org.br";
            request.Referer = "https://www.falecidosnobrasil.org.br/";
            request.UserAgent = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/117.0.0.0 Safari/537.36";
            request.ContentLength = parametros.Length;
            Stream sw = request.GetRequestStream();
            sw.Write(byteArray, 0, byteArray.Length);
            HttpWebResponse response = (HttpWebResponse)request.GetResponse();
            using (StreamReader sr = new StreamReader(response.GetResponseStream(), Encoding.Default))
            {
                htmlResult = sr.ReadToEnd();
            }

            return htmlResult;
        }

    }
}

