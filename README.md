# Resposta-Questao-2


using System;
using System.Net.Http;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq;

class Program
{
    // URL base da API
    const string apiUrl = "https://jsonmock.hackerrank.com/api/football_matches";

    static async Task<int> GetGoalsForTeamAsync(string team, int year)
    {
        int totalGoals = 0;
        int currentPage = 1;
        int totalPages;

        using (HttpClient client = new HttpClient())
        {
            // Verificando os jogos onde o time é o "team1" (jogou em casa)
            do
            {
                // Montar a URL para consultar a API
                string url = $"{apiUrl}?year={year}&team1={team}&page={currentPage}";
                HttpResponseMessage response = await client.GetAsync(url);
                string jsonString = await response.Content.ReadAsStringAsync();

                // Parse da resposta em JSON
                JObject jsonResponse = JObject.Parse(jsonString);
                totalPages = (int)jsonResponse["total_pages"];
                
                // Somar os gols marcados como "team1"
                foreach (var match in jsonResponse["data"])
                {
                    int team1Goals = int.Parse(match["team1goals"].ToString());
                    totalGoals += team1Goals;
                }

                currentPage++;
            } while (currentPage <= totalPages);

            // Reiniciando variáveis para consultar jogos onde o time é "team2" (jogou fora de casa)
            currentPage = 1;

            // Verificando os jogos onde o time é o "team2"
            do
            {
                // Montar a URL para consultar a API
                string url = $"{apiUrl}?year={year}&team2={team}&page={currentPage}";
                HttpResponseMessage response = await client.GetAsync(url);
                string jsonString = await response.Content.ReadAsStringAsync();

                // Parse da resposta em JSON
                JObject jsonResponse = JObject.Parse(jsonString);
                totalPages = (int)jsonResponse["total_pages"];
                
                // Somar os gols marcados como "team2"
                foreach (var match in jsonResponse["data"])
                {
                    int team2Goals = int.Parse(match["team2goals"].ToString());
                    totalGoals += team2Goals;
                }

                currentPage++;
            } while (currentPage <= totalPages);
        }

        return totalGoals;
    }

    static async Task Main(string[] args)
    {
        // Exemplo 1: Paris Saint-Germain em 2013
        string team1 = "Paris Saint-Germain";
        int year1 = 2013;
        int goalsTeam1 = await GetGoalsForTeamAsync(team1, year1);
        Console.WriteLine($"Team {team1} scored {goalsTeam1} goals in {year1}");

        // Exemplo 2: Chelsea em 2014
        string team2 = "Chelsea";
        int year2 = 2014;
        int goalsTeam2 = await GetGoalsForTeamAsync(team2, year2);
        Console.WriteLine($"Team {team2} scored {goalsTeam2} goals in {year2}");
    }
}

