1. Přihlaste se na [Azure Portal].
2. Vyberte **+ nový** > **Web + mobilní** > **mobilní aplikace**a pak zadejte název pro back-end mobilní aplikace.
3. Pro **skupiny prostředků**, vyberte existující skupinu prostředků nebo vytvořte novou (pomocí stejný název jako vaše aplikace). 
4. Pro **plán služby App Service**, výchozí plán (v [úrovně Standard](https://azure.microsoft.com/pricing/details/app-service/)) je vybrána. Můžete také vybrat jiný plán nebo [vytvořte novou](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   Určit nastavení plánu služby App Service [umístění, funkce, náklady a výpočetní prostředky](https://azure.microsoft.com/pricing/details/app-service/) přidružené k vaší aplikaci. Další informace o službě App Service plány a postup vytvoření nového plánu v různých cenových vrstvy a v požadovaném umístění najdete v části [podrobný přehled plánů služby Azure App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
5. Vyberte **Vytvořit**. Tento krok vytvoří back-end mobilní aplikace. 
6. V **nastavení** pro nové Mobile Apps back-end, vyberte **úvodní** > aplikační platforma vašeho klienta > **připojit databázi**. 
   
   ![Výběr připojení databáze](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. V **přidat datové připojení** podokně, vyberte **SQL Database** > **vytvořit novou databázi**. Zadejte název databáze, zvolte cenovou úroveň a potom vyberte **Server**. Novou databázi můžete použít opakovaně. Pokud už ve stejném umístění databázi máte, můžete místo toho vybrat možnost **Použít existující databázi**. Nedoporučujeme používání databáze v jiném umístění, z důvodu náklady na šířku pásma a zhorší latenci.
   
   ![Výběr databáze](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. V **nový server** podokně, zadejte název jedinečné serveru v **název serveru** zadejte přihlašovací jméno a heslo, vyberte **služeb Azure povolit přístup k serveru**a vyberte  **OK**. Tento krok vytvoří novou databázi.
9. Zpět v **přidat datové připojení** podokně, vyberte **připojovací řetězec**, zadejte hodnoty přihlašovací jméno a heslo pro vaši databázi a vyberte **OK**. 

   Počkejte několik minut databáze, která se úspěšně nasazena, než budete pokračovat.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
