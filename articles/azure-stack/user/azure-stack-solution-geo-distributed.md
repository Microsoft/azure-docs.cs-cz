---
title: Vytvoření řešení geograficky distribuované aplikace s využitím Azure a Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit řešení geograficky distribuované aplikace s využitím Azure a Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: da6c9fa416d19916243860178d15619306aaf3c0
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54305311"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-with-azure-and-azure-stack"></a>Kurz: Vytvoření řešení geograficky distribuované aplikace s využitím Azure a Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Zjistěte, jak směrovat provoz na konkrétní koncové body na základě různých metrik pomocí vzoru geograficky distribuované aplikace. Vytváření Traffic Manageru profil s geografické konfigurace založená na směrování a koncového bodu zajišťuje informace se směruje do koncových bodů na základě místní požadavky, podnikové a mezinárodní nařízení a dat, které potřebujete.

V tomto kurzu vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Vytvoření geograficky distribuované aplikace.
> - Traffic Manager můžete používat cílit své aplikace.

## <a name="use-the-geo-distributed-apps-pattern"></a>Použití vzoru geograficky distribuované aplikace

Se vzorkem geograficky distribuované můžete aplikace zahrnuje oblasti. Použít výchozí do veřejného cloudu, ale někteří uživatelé můžou vyžadovat, že svá data zůstanou v jejich oblasti. Může směrovat uživatele do nejvhodnějšího cloudu podle svých požadavků.

### <a name="issues-and-considerations"></a>Problémy a důležité informace

#### <a name="scalability-considerations"></a>Aspekty zabezpečení

Řešení, které vytvoříte v tomto kurzu se zohlednit škálovatelnost. Nicméně pokud použijete v kombinaci s dalšími řešeními a technologie Azure a místní zvládne požadavky na škálovatelnost. Informace týkající se vytvoření hybridní řešení s automatickým Škálováním pomocí traffic Manageru najdete v tématu [vytvářet řešení škálování cloudu s Azure](azure-stack-solution-cloud-burst.md).

#### <a name="availability-considerations"></a>Aspekty dostupnosti

Stejně jako v případě s aspekty zabezpečení, toto řešení se nezabývá přímo dostupnosti. Ale také podobně jako naše důležité informace ke škálovatelnosti, Azure a v místním technologie a řešení můžete implementovat v rámci tohoto řešení k zajištění vysoké dostupnosti pro všechny součásti, které jsou zahrnuté.

### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít

- Vaše organizace má mezinárodní vyžadující vlastní místní zabezpečení a distribuci zásad větví.

- Jednotlivým pobočkám organizace si vyžádá zaměstnance, firmy a data, zařízení, která vyžadují reporting aktivita na místní předpisy a časovém pásmu.

- Vodorovně horizontální navýšení kapacity aplikace s více nasazení aplikací pro zpracování požadavků na extrémní zatížení prováděných v rámci jedné oblasti i napříč oblastmi, mohou být splněny požadavky na vysokou škálovatelností.

### <a name="planning-the-topology"></a>Plánování topologie

Před vytvoření distribuované aplikace nároky, pomáhá nemají následující informace:

-   **Vlastní doména aplikace:** Co je vlastní název domény, který zákazníci budou používat pro přístup k aplikaci? Pro ukázkovou aplikaci vlastní název domény je *www.scalableasedemo.com.*

-   **Doménu Traffic Manageru:** Název domény je potřeba zvolit při vytváření [profilu Azure Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles). Tento název se zkombinuje s *trafficmanager.net* příponu je třeba zaregistrovat položku domény, který je spravovaný nástrojem Traffic Manager. Ukázkové aplikace je název zvoleném *škálovatelné služby ase ukázka*. V důsledku toho úplný název domény, který je spravovaný nástrojem Traffic Manager je *škálovatelné služby ase demo.trafficmanager.net*.

-   **Strategie pro škálování app nároky:** Bude nároky na aplikaci distribuovat napříč více App Service Environment v jedné oblasti? Více oblastech? Kombinací obou metod? Rozhodnutí by podle očekávání, kde budou pocházet provozu zákazníka a také jak můžete škálovat zbývající aplikace podporu back-end infrastrukturu. Například se 100 % bezstavové aplikace, aplikace je možné masivně škálovat pomocí kombinace více App Service Environment v jedné oblasti Azure, vynásobený nasazení ve víc oblastech Azure App Service Environment. Pomocí 15 + globálními oblastmi Azure si můžete vybrat z zákazníků, kteří vytvářejí skutečně nároky na celém světě vysoce škálovatelné aplikace. Pro ukázková aplikace používá pro účely tohoto článku byly vytvořeny tři App Service Environment v jedné oblasti Azure (střed USA – jih).

-   **Zásady vytváření názvů pro App Service Environment:** Každá služba App Service Environment vyžaduje jedinečný název. Nad rámec jednu nebo dvě služby App Service Environment je vhodné používat takové názvy vám pomůže identifikovat každou službu App Service Environment. Ukázkové aplikace byl použit jednoduché zásady vytváření názvů. Názvy tři App Service Environment jsou *fe1ase*, *fe2ase*, a *fe3ase*.

-   **Zásady vytváření názvů pro aplikace:** Protože více instancí aplikace se nasadí, název je potřeba pro každou instanci nasazené aplikace. App Service Environment v stejný název aplikace je možné napříč více App Service Environment. Protože každá služba App Service Environment má příponu domény jedinečný, vývojáři můžete opakovaně používat přesně stejný název aplikace v každém prostředí. Například vývojář může mít aplikace s názvem následujícím způsobem: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*atd. Každá instance aplikace pro aplikaci v tomto scénáři má jedinečný název. Názvy instancí aplikace používá jsou *webfrontend1*, *webfrontend2*, a *webfrontend3*.

> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí a povolení ten jediný hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace kdekoli.  
> 
> Dokument White Paper [aspekty návrhu pro hybridní aplikace](https://aka.ms/hybrid-cloud-applications-pillars) kontroly pro navrhování, nasazování a provozování pilířů kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) hybridní aplikace. Aspekty návrhu při optimalizaci návrhu hybridní aplikace, minimalizovat problémy v produkčním prostředí.

## <a name="part-1-create-a-geo-distributed-app"></a>Část 1: Vytvoření geografické distribuce aplikace

V této části vytvoříte webovou aplikaci.

> [!div class="checklist"]
> - Vytvoření webové aplikace a publikování
> - Přidání kódu do úložiště Azure
> - Sestavení aplikace přejděte na několik cloudových cílů.
> - Spravovat a konfigurovat proces průběžného Doručování

### <a name="prerequisites"></a>Požadavky

Předplatné Azure a instalace služby Azure Stack se vyžadují.

### <a name="geo-distributed-app-steps"></a>Kroky geografické distribuce aplikace

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Získat vlastní doménu a konfigurace DNS

Aktualizace souboru zóny DNS pro doménu. Azure AD pak můžete ověřit vlastnictví názvu vlastní domény. Použití [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) Azure nebo Office 365/externí záznamů DNS v rámci Azure, nebo přidejte položku DNS na [různých registrátora DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Zaregistrujte vlastní doménu veřejného registru.

2. Přihlaste se k registrátorovi názvu domény. Schválené správcem, může být nutné provést aktualizace DNS.

3. Aktualizace souboru zóny DNS pro doménu tak, že přidáte položku DNS poskytuje Azure AD. Položku DNS nedojde ke změně chování například směrování pošty nebo webhosting.

### <a name="create-web-apps-and-publish"></a>Vytvoření webové aplikace a publikování

Nastavení hybridní CI/CD a nasazení webové aplikace do Azure a Azure Stack a předávání změn pro oba cloudy, auto.

> [!Note]  
> Azure Stack pomocí správné imagí syndikovat do běhu (Windows Server a SQL) a nasazení služby App Service jsou povinné. Projděte si dokumentaci k App Service [před zahájením práce s App Service ve službě Azure Stack](../azure-stack-app-service-before-you-get-started.md) oddílu pro operátor Azure stacku.

#### <a name="add-code-to-azure-repos"></a>Přidání kódu do úložiště Azure

1. Přihlaste se k programu Visual Studio pomocí **účet, který má práva k vytvoření projektu** na úložiště Azure.

    Hybridní průběžné integrace a průběžného doručování (CI/CD) můžete použít kód aplikace a kódu infrastruktury. Použití [šablon Azure Resource Manageru](https://azure.microsoft.com/resources/templates/) i privátní a prostředí pro vývoj pro cloud.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image1.JPG)

2. **Naklonujte úložiště** ve vytváření a otevírání výchozí webové aplikace.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>Vytvoření nasazení webové aplikace v obou cloudy

1.  Upravit **WebApplication.csproj** souboru: Vyberte **Runtimeidentifier** a přidejte **win10 x64**. (Viz [Self-contained nasazení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentaci.)

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image3.png)

1.  **Vrátit se změnami kódu do úložiště Azure** pomocí Team Exploreru.

2.  Ujistěte se, že **kód aplikace** byly vráceny do úložiště Azure.

### <a name="create-the-build-definition"></a>Vytvořte definici sestavení

1. **Přihlaste se k Azure kanály** potvrďte schopnost vytvářet definice sestavení.

2. Přidat **- r win10-x64** kódu. To je potřeba aktivovat samostatná nasazení s.Net Core.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image4.png)

3. **Spuštění sestavení**. [Samostatná nasazení sestavení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) procesu budete publikovat artefakty, které lze spustit v Azure a Azure Stack.

**Pomocí Azure hostovaný Agent**

Pomocí hostovaný agent v kanálech Azure je vhodná možnost vytvářet a nasazovat webové aplikace. Údržba a upgrady automaticky provádí Microsoft Azure, takže se vzhledem, bez přerušení vývoj, testování a nasazení.

### <a name="manage-and-configure-the-cd-process"></a>Spravovat a konfigurovat proces průběžného Doručování

Azure DevOps a Azure DevOps serveru poskytovat vysoce konfigurovatelné a spravovatelné kanálu pro vydané verze do více prostředí, jako je vývoj, Fázování importu, dotazů a odpovědí a produkční prostředí; včetně, která vyžadují schválení určitým fázím.

#### <a name="create-release-definition"></a>Vytvořte definici vydané verze


![Alternativní text](media/azure-stack-solution-geo-distributed/image5.png)

1.  Vyberte **plus** tlačítko pro přidání nové vydané verze v části **kartě vydané verze** na stránce sestavení a vydaná verze sady Visual Studio Online (VSO).

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image6.png)

2. Použít **nasazení služby Azure App Service** šablony.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image7.png)

3. V části přidat artefakt rozevírací nabídky **přidat artefakt** pro sestavení aplikace cloudu Azure.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image8.png)

4. Na kartě kanálu, vyberte **fáze, úloha** odkaz prostředí a nastavte hodnoty prostředí cloudu Azure.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image9.png)

5. Nastavte **název prostředí** a vyberete platformu Azure **předplatné** pro koncový bod cloudu Azure.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image10.png)

6. V části název prostředí, nastavte požadovaný **název služby Azure app service**.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image11.png)

7. Zadejte **hostované VS2017** pod frontu agenta pro prostředí Azure hostované v cloudu.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image12.png)

8. V nabídce nasazení služby Azure App Service, vyberte platnými **balíčku nebo složky** pro prostředí. Kliknutím na tlačítko OK **umístění složky**.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image13.png)

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image14.png)

9. Uložte všechny změny a vraťte se do **kanál pro vydávání verzí**.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image15.png)

10. Přidat **nové artefaktů** výběr sestavení pro aplikaci služby Azure Stack.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image16.png)

11. Přidejte jeden další použití prostředí **nasazení služby Azure App Service.**

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image17.png)

12. Název nového prostředí **Azure Stack.**

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image18.png)

13. Najít prostředí Azure Stack v rámci **úloh** kartu.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image19.png)

14. Vyberte **předplatné** pro koncový bod služby Azure Stack.

  ![Alternativní text](media/azure-stack-solution-geo-distributed/image20.png)

15. Nastavte název webové aplikace služby Azure Stack jako **název služby App service**.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image21.png)

16. Vyberte **agenta služby Azure Stack**.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image22.png)

17. V části nasazení Azure App Service vyberte oddíl platnými **balíčku nebo složky** pro prostředí. Kliknutím na tlačítko OK **umístění složky**.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image23.png)

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image24.png)

18. V části **proměnnou** karta přidat proměnnou s názvem `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, nastavte ho na hodnotu jako `true`a rozsah `Azure Stack`.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image25.png)

19. Vyberte **průběžné** ikona aktivační události nasazení v artefakty a povolit **Continues** aktivační události nasazení.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image26.png)

20. Vyberte **před nasazením** ikonu podmínky v prostředí Azure Stack a nastavte aktivační události na **po vydání.**

21. Uložte všechny změny.

> [!Note]  
>  Některá nastavení pro úlohy může automaticky definovaná jako [proměnné prostředí](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) při vytvoření definice verze ze šablony. Tato nastavení nelze změnit v nastavení úkolu; Místo toho musíte vybrat nadřazená položka prostředí upravit tato nastavení.

## <a name="part-2-update-web-app-options"></a>Část 2: Aktualizace webové aplikace možnosti

[Azure App Service ](https://docs.microsoft.com/azure/app-service/overview) je vysoce škálovatelná služba s automatickými opravami pro hostování webů. 

![Alternativní text](media/azure-stack-solution-geo-distributed/image27.png)

> [!div class="checklist"]
> - Mapování existujícího vlastního názvu DNS na Azure Web Apps
> - Použití ** záznam CNAME **záznam** k mapování vlastního názvu DNS do služby App Service.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mapování existujícího vlastního názvu DNS na Azure Web Apps

> [!Note]  
>  Používejte záznam CNAME pro všechny vlastní názvy DNS kromě kořenové domény (pro example,northwind.com).

Pokud chcete do služby App Service migrovat živý web a jeho název domény DNS, přečtěte si téma [Migrace aktivního názvu DNS do služby Azure App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain).

### <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

-   [Vytvoření aplikace služby App Service](https://docs.microsoft.com/azure/app-service/), nebo použít aplikaci vytvořili pro účely jiného kurzu.

-   Zakoupit název domény a zajistit přístup k registru DNS poskytovatele domény.

Aktualizace souboru zóny DNS pro doménu. Azure AD ověří vlastnictví vlastního názvu domény. Použití [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) Azure nebo Office 365/externí záznamů DNS v rámci Azure, nebo přidejte položku DNS na [různých registrátora DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

-   Zaregistrujte vlastní doménu veřejného registru.

-   Přihlaste se k registrátorovi názvu domény. (Schválené správce může být potřeba ke zpřístupnění aktualizací DNS.)

-   Aktualizace souboru zóny DNS pro doménu tak, že přidáte položku DNS poskytuje Azure AD.

Například mohli přidat záznamy DNS pro northwindcloud.com a www.northwindcloud.com, nakonfigurujte nastavení DNS pro kořenovou doménu northwindcloud.com.

> [!Note]  
>  Název domény je možné zakoupit pomocí [webu Azure portal](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).  
> Abyste mohli mapovat vlastní název DNS na webovou aplikaci, [plán služby App Service](https://azure.microsoft.com/pricing/details/app-service/) příslušné webové aplikace musí být na placené úrovni (**Shared**, **Basic**, **Standard** nebo **Premium**).



### <a name="create-and-map-cname-and-a-records"></a>Vytvoření a mapování CNAME záznamy a A

#### <a name="access-dns-records-with-domain-provider"></a>Přístup k záznamům DNS u poskytovatele domény

> [!Note]  
>  Použití Azure DNS nakonfigurovat vlastní název DNS pro Azure Web Apps. Další informace najdete v tématu popisujícím [použití Azure DNS k určení nastavení vlastní domény pro službu Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain).

1.  Přihlaste se k webu hlavní zprostředkovatele.

2.  Vyhledejte stránku pro správu záznamů DNS. Každý poskytovatel domén má vlastní rozhraní pro záznamy DNS. Hledejte oblasti webu označené jako **Domain Name** (Název domény), **DNS** nebo **Name Server Management** (Správa názvových serverů).

Stránka záznamů DNS, lze zobrazit v **Moje domény**. Hledání propojení s názvem **souboru zóny**, **záznamy DNS**, nebo **pokročilou konfiguraci**.

Následující snímek obrazovky obsahuje příklad stránky záznamů DNS:

![Příklad stránky záznamů DNS](media/azure-stack-solution-geo-distributed/image28.png)

1.  V registrátora názvu domény, vyberte **přidat nebo vytvořit** k vytvoření záznamu. Někteří poskytovatelé nabízejí různé odkazy pro přidání různých typů záznamů. V dokumentaci poskytovatele.

2.  Přidejte záznam CNAME pro mapování subdomény na výchozí název hostitele aplikace.

  Pro příklad domény www.northwindcloud.com přidejte záznam CNAME, který mapuje název na < aplikace\_název >. azurewebsites.net.

Po přidání záznamu CNAME bude stránka záznamů DNS vypadat jako v následujícím příkladu:

![Přechod do aplikace Azure na portálu](media/azure-stack-solution-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Povolení mapování záznamu CNAME v Azure

1.  V nové záložce Přihlaste se k webu Azure portal

2.  Přejděte do služby App Services.

3.  Vyberte webovou aplikaci.

4.  V levém navigačním panelu na stránce aplikace na webu Azure Portal vyberte **Vlastní domény**.

5.  Vyberte ikonu **+** vedle možnosti **Přidat název hostitele**.

1.  Zadejte plně kvalifikovaný název, například `www.northwindcloud.com`.

2.  Vyberte **Ověřit**.

3.  Pokud je uvedeno, přidat další záznamy z ostatních typů (`A` nebo `TXT`) záznamů DNS registrátorů název domény. Azure vám poskytne hodnoty a typy těchto záznamů:

    a.  Záznam **A** pro mapování na IP adresu aplikace.

    b.  A **TXT** záznam pro mapování na výchozí název hostitele aplikace < název_aplikace >. azurewebsites.net. Služba App Service používá tento záznam pouze během konfigurace k ověření vlastnictví vlastní domény. Po ověření záznam TXT odstraňte.

4.  Dokončení této úlohy v kartě registrátora domény a odhlášením až **přidat název hostitele** se aktivuje tlačítko.

5.  Ujistěte se, že ** typ záznamu názvu hostitele je nastavena **CNAME (www.example.com nebo jakákoli subdoména)**.

6.  Vyberte **Přidat název hostitele**.

7.  Zadejte plně kvalifikovaný název, například `northwindcloud.com`.

8.  Vyberte **Ověřit**.

9.  **Přidat** aktivován.

10. Ujistěte se, že ** typ záznamu názvu hostitele je nastavena **záznam A (www.example.com)**.

11. **Přidat název hostitele**.

  Může trvat nějakou dobu nové názvy hostitelů se projeví v aplikaci prvku **vlastní domény** stránky. Zkuste aktualizovat prohlížeč, aby se data aktualizovala.
  
  ![Alternativní text](media/azure-stack-solution-geo-distributed/image31.png) 
  
  V případě chyby se v dolní části stránky zobrazí oznámení o chybě ověření. ![Chyba ověření](media/azure-stack-solution-geo-distributed/image32.png)

> [!Note]  
>  Výše uvedené kroky může opakovat mapovat zástupnou doménu (\*. northwindcloud.com)... To umožňuje přidání všechny další subdomény, které do této služby app service bez nutnosti vytvářet samostatné záznam CNAME pro každou z nich. Doménový Registrátor pokynů ke konfiguraci tohoto nastavení.

#### <a name="test-in-a-browser"></a>Otestovat v prohlížeči

Přejděte na názvy DNS nakonfigurovali v předchozích krocích (například `northwindcloud.com`, www.northwindcloud.com.

## <a name="part-3-bind-a-custom-ssl-cert"></a>3. část: Vytvořit vazbu vlastního certifikátu SSL

V této části:

> [!div class="checklist"]
> - Vytvořit vazbu vlastního certifikátu SSL do služby App Service
> - Vynucení protokolu HTTPS pro aplikaci
> - Automatizace vytváření vazeb certifikátů SSL pomocí skriptů

> [!Note]  
> V případě potřeby zákazník získat certifikát SSL na webu Azure Portal a jeho vazbu na webovou aplikaci. Postupujte podle [kurzu Certifikáty App Service](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site).

### <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

-   [Vytvořit aplikaci App Service](https://docs.microsoft.com/azure/app-service/)
-   [Namapovat na svou webovou aplikaci vlastní název DNS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   Získat certifikát SSL od důvěryhodné certifikační autority a pomocí klíče k podepsání žádosti

### <a name="requirements-for-your-ssl-certificate"></a>Požadavky na certifikát SSL

Pokud chcete ve službě App Service použít certifikát, musí splňovat všechny následující požadavky:

-   Podepsaný důvěryhodnou certifikační autoritou

-   Exportovaný jako soubor PFX chráněný heslem

-   Obsahuje privátní klíč dlouhý alespoň 2 048 bitů

-   Obsahuje v řetězu certifikátů všechny zprostředkující certifikáty

> [!Note]  
>  **Eliptické certifikáty ECC (Elliptic Curve Cryptography)** pracovat se službou App Service, ale nejsou zahrnuté v této příručce. Získáte pomoc s vytvořením certifikáty ECC certifikační autority. 

#### <a name="prepare-the-web-app"></a>Příprava webové aplikace

Vytvořit vazbu vlastního certifikátu SSL do webové aplikace [plán služby App Service](https://azure.microsoft.com/pricing/details/app-service/) musí být v **základní**, **standardní**, nebo **Premium** vrstvy.

#### <a name="sign-in-to-azure"></a>Přihlásit se k Azure

1.  Otevřít [webu Azure portal](https://portal.azure.com/) a přejděte do webové aplikace.

2.  V nabídce vlevo vyberte **App Services**a pak vyberte název webové aplikace.

![Výběr webové aplikace](media/azure-stack-solution-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

1.  V levém navigačním panelu na stránce webové aplikace, přejděte **nastavení** a vyberte **vertikálně navýšit kapacitu (plán služby App Service)**.

    ![Nabídka Vertikálně navýšit kapacitu](media/azure-stack-solution-geo-distributed/image34.png)

1.  Zkontrolujte webové aplikace není **Free** nebo **Shared** vrstvy. Aktuální úroveň webové aplikace je zvýrazněná tmavě modrá pole.

    ![Kontrola cenové úrovně](media/azure-stack-solution-geo-distributed/image35.png)

Na úrovni **Free** a **Standard** se nepodporuje vlastní SSL. Upscale, postupujte podle kroků v další části, nebo **volba cenové úrovně** stránce a přeskočte k [nahrání a vytvoření vazby certifikátu SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

#### <a name="scale-up-your-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

1.  Vyberte některou z úrovní **Basic**, **Standard** nebo **Premium**.

2.  Vyberte **vyberte**.

![Výběr cenové úrovně](media/azure-stack-solution-geo-distributed/image36.png)

Operace škálování je dokončená, když se zobrazí oznámení.

![Oznámení vertikálního navýšení kapacity](media/azure-stack-solution-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>Vytvoření vazby certifikátu SSL a sloučení zprostředkujících certifikátů

Sloučení více certifikátů v řetězu certifikátů.

1. **Otevřete jednotlivé certifikáty** jste obdrželi v textovém editoru.

2. Vytvořte soubor *mergedcertificate.crt* pro sloučený certifikát. V textovém editoru zkopírujte do tohoto souboru obsah jednotlivých certifikátů. Pořadí certifikátů by mělo odpovídat jejich pořadí v řetězu certifikátů, počínaje vaším certifikátem a konče kořenovým certifikátem. Soubor bude vypadat jako v následujícím příkladu:

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>Export certifikátu do formátu PFX

Exportujte sloučený certifikát SSL s privátním klíčem vygenerovaný certifikát.

Soubor privátního klíče se vytvoří pomocí OpenSSL. Exportujte certifikát do formátu PFX, spusťte následující příkaz a nahraďte zástupné symboly *< private-key-file >* a *< sloučit certificate-file >* s cestami k privátní klíče a sloučení soubor certifikátu.

```PowerShell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Po zobrazení výzvy definujte heslo pro export pro nahrávání certifikátu SSL do služby App Service později.

Když služba IIS nebo **Certreq.exe** se používají ke generování požadavku na certifikát, nainstalujte certifikát do místního počítače a pak [exportujte certifikát do formátu PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

#### <a name="upload-the-ssl-certificate"></a>Nahrát certifikát SSL

1.  Vyberte **nastavení SSL** v levém navigačním panelu webové aplikace.

2.  Vyberte **nahrát certifikát**.

3.  V **soubor certifikátu PFX**, vyberte soubor PFX.

4.  4. V **heslo certifikátu**, zadejte heslo, které vytvořili při exportování souboru PFX.

5.  Vyberte **Nahrát**.

![Nahrání certifikátu](media/azure-stack-solution-geo-distributed/image38.png)

Jakmile App Service dokončí nahrávání certifikátu, zobrazí se v **nastavení SSL** stránky.

![Alternativní text](media/azure-stack-solution-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>Vytvoření vazby certifikátu SSL

1.  V **vazby SSL** vyberte **přidat vazbu**.

    > [!Note]  
    >  Pokud tento certifikát se odeslal, ale nezobrazí názvy domén v **Hostname** rozevírací seznam, zkuste aktualizovat stránku v prohlížeči.

1.  V **přidat vazbu SSL** stránky, použijte rozevírací nabídky vyberte název domény pro zabezpečení a certifikát, který chcete použít.

2.  V **typ SSL**, vyberte, jestli se má použít [ **indikace názvu serveru (SNI)**](http://en.wikipedia.org/wiki/Server_Name_Indication)nebo založené na protokolu IP SSL.

-   **Na základě SNI SSL**-lze přidat více SSL typu sni vazby. Tato možnost umožňuje zabezpečení několika domén na stejné IP adrese pomocí několika certifikátů SSL. Většina moderních prohlížečů (včetně prohlížečů Internet Explorer, Chrome, Firefox a Opera) podporuje SNI (ucelenější informace o podpoře prohlížečů najdete v článku o [Indikaci názvu serveru](http://wikipedia.org/wiki/Server_Name_Indication)).

-   **Založené na protokolu IP SSL**-lze přidat pouze jednu vazbu SSL na základě IP adresy. Tato možnost umožňuje zabezpečení vyhrazené veřejné IP adresy pouze jedním certifikátem SSL. Pokud chcete zabezpečit více domén, je zabezpečte všechny používají stejný certifikát protokolu SSL. Toto je tradiční možnost vytvoření vazby SSL.

    1.  Vyberte **přidat vazbu**.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image40.png)

Jakmile App Service dokončí nahrávání certifikátu, zobrazí se v **vazby SSL** oddíly.

![Alternativní text](media/azure-stack-solution-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>Přemapujte záznam A pro IP SSL.

Pokud ve webové aplikaci se používá protokol SSL na základě IP adresy, přeskočte k [Test HTTPS pro vlastní doménu](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

Ve výchozím nastavení webová aplikace používá sdílenou veřejnou IP adresu. Když tento certifikát je vytvořena vazba s protokolem SSL na základě IP adresy, App Service vytvoří nové a vyhrazené IP adresy pro webovou aplikaci.

Při záznamu A je namapována na webovou aplikaci, musí aktualizovat registr domény s vyhrazenou IP adresu.

**Vlastní domény** stránka je aktualizována novou vyhrazenou IP adresu. Zkopírujte tento [IP adresu](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain), pak přemapovat [záznam](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) na tuto novou IP adresu.

#### <a name="test-https"></a>Test HTTPS

V různých prohlížečích zkontrolujte procházet https://<your.custom.domain>to webu, který poskytuje přístupový bod.

![Alternativní text](media/azure-stack-solution-geo-distributed/image42.png)

> [!Note]  
> Pokud dojde k chybám ověření certifikátu, příčinou může být certifikát podepsaný svým držitelem nebo zprostředkující certifikáty byly ponechat při exportu do souboru PFX.

#### <a name="enforce-https"></a>Vynucení HTTPS

Ve výchozím nastavení kdokoli může přístup k webové aplikaci pomocí HTTP. může přesměrovat všechny požadavky HTTP na HTTPS port.

Na stránce webové aplikace, vyberte **SL nastavení**. Pak v části **Pouze HTTPS** vyberte **Zapnuto**.

![Vynucení HTTPS](media/azure-stack-solution-geo-distributed/image43.png)

Po dokončení operace přejděte na jakoukoli adresu URL HTTP odkazující na aplikaci. Příklad:

-   http://<app_name>.azurewebsites.net
-   http://northwindcloud.com
-   <http://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>Vynucení protokolu TLS 1.1/1.2

Umožňuje aplikaci [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 ve výchozím nastavení, která je již nejsou považované za bezpečné hlediska oborových standardů, jako například [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Pokud chcete vynucovat vyšší verze protokolu TLS, postupujte následovně:

1.  Na stránce webové aplikace v levém navigačním panelu vyberte **nastavení SSL**.

2.  V **verze protokolu TLS**, vyberte minimální verzi TLS.

![Vynucení protokolu TLS 1.1 nebo 1.2](media/azure-stack-solution-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

1.  Vyberte **vytvořit prostředek** > **sítě** > **profil služby Traffic Manager** > **vytvořit**.

2.  Část **Vytvořit profil služby Traffic Manager** vyplňte následovně:

    1.  V **název**, zadejte název pro profil. Tento název musí být jedinečný v rámci zóny manager.net provoz a výsledky v názvu DNS, manager.net provoz, který se používá pro přístup k profilu služby Traffic Manager.

    2.  V **metodu směrování**, vyberte **Geographicrouting metoda**.

    3.  V **předplatné**, vyberte předplatné, pod kterým chcete profil vytvořit.

    4.  V části **Skupina prostředků** vytvořte novou skupinu prostředků, do které chcete profil umístit.

    5.  V poli **Umístění skupiny prostředků** vyberte umístění skupiny prostředků. Toto nastavení označuje umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manageru, který se nasadí globálně.

    6.  Vyberte **Vytvořit**.

    7.  Po dokončení globálního nasazení profilu služby Traffic Manager je uveden v příslušné skupině prostředků jako jeden z prostředků.

    ![Alternativní text](media/azure-stack-solution-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

1.  Na panelu hledání portálů, vyhledejte ** profil služby Traffic Manager ** název vytvořený v předchozí části a vyberte profil služby traffic manager ve výsledcích, který je zobrazeno.

2.  V **profil služby Traffic Manager**v **nastavení** vyberte **koncové body**.

3.  Vyberte **Přidat**.

4.  Přidává se koncový bod služby Azure Stack.

5.  Pro **typ**vyberte **externí koncový bod**.

6.  Zadejte **název** pro tento koncový bod v ideálním případě název služby Azure Stack.

7.  Pro plně kvalifikovaný název domény (**plně kvalifikovaný název domény**), použijte externí adresu URL pro webovou aplikaci Azure Stack.

8.  V části geomapping, vyberte oblast/kontinent, kde je umístěný prostředek, třeba **Evropa.**

9.  V zemi nebo oblast rozevíracího seznamu, který se zobrazí, vyberte zemi, která se použije k tomuto koncovému bodu, například **Německo**.

10. Políčko **Přidat jako zakázaný** ponechte nezaškrtnuté.

11. Vyberte **OK**.

12. Přidat koncový bod Azure:

    1.  Pro **typ**vyberte **koncový bod Azure**.

    2.  Zadejte **název** pro tento koncový bod.

    3.  Pro **typ cílového prostředku**vyberte **služby App Service**.

    4.  Pro **cílový prostředek**vyberte **vybrat aplikační službu** zobrazíte seznam webových aplikací ve stejném předplatném. V **prostředků**, vyberte použití App service jako první koncový bod.

13. V části geomapování vyberte oblast/kontinent, kde je umístěný prostředek, třeba **Severní Amerika/střední Amerika/Karibská oblast.**

14. V zemi nebo oblast rozevíracího seznamu, který se zobrazí nechte pole prázdné, chcete-li vybrat všechny výše uvedené oblastní seskupení.

15. Políčko **Přidat jako zakázaný** ponechte nezaškrtnuté.

16. Vyberte **OK**.

  > [!Note]  
  >  Vytvořte aspoň jeden koncový bod s geografické oboru všechny (celý svět) která bude sloužit jako výchozí koncový bod pro prostředek.

1.  Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

  ![Alternativní text](media/azure-stack-solution-geo-distributed/image46.png)

**Globální organizace spoléhá na funkce Azure geografická distribuce**

Směrování provozu dat prostřednictvím Azure Traffic Manageru a koncové body konkrétní zeměpisné oblasti umožňuje globální podniky, které místní předpisy a ponechat data kompatibilní a zabezpečené zásadní pro úspěch firmy místní a ve vzdálených umístěních.

## <a name="next-steps"></a>Další postup

- Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
