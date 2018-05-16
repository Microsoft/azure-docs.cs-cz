---
title: 'Kurz: DevOps pomocí portálu Azure | Microsoft Docs'
description: Poznejte různé pracovní postupy pro DevOps v portálu Azure.
services: azure-portal
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: 4f1c5bc1-c732-4d35-b5df-0fd68e547d38
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2016
ms.author: mlearned
ms.openlocfilehash: 7fb3de72e28b19f9ca30968b267bf985ae384275
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-devops-with-the-azure-portal"></a>Kurz: DevOps pomocí portálu Azure
Platforma Azure je plná flexibilních pracovních postupů DevOps. V tomto kurzu zjistěte, jak využít možnosti portálu Azure k vývoji, testování, nasazení, řešení potíží s, monitorování a správě spuštěných aplikací. Tento kurz se zaměřuje na:

1. Vytvoření webové aplikace a povolení průběžného nasazování
2. Vývoj a testování aplikace
3. Sledování a řešení potíží s aplikací
4. Úlohy správy obecné aplikace

## <a name="creating-a-web-app-and-enabling-continuous-deployment"></a>Vytvoření webové aplikace a povolení průběžného nasazování
Vytvořte webovou aplikaci pomocí služby [Azure App Service](https://azure.microsoft.com/services/app-service/), budete ji používat v dalších částech tohoto kurzu. Průběžné nasazování budete nejprve povolit z vašeho úložiště zdrojového kódu do prostředí provozu Azure.

1. Přihlaste se k portálu Azure
2. Vyberte **App Services** &gt; **Přidat ikonu** a zadejte název, vyberte předplatné a vytvořte novou skupinu prostředků, která bude sloužit jako kontejner pro službu.
   
   Skupiny prostředků vám umožňují spravovat různé aspekty řešení například k fakturaci, nasazení a sledování všech jako jednu skupinu prostřednictvím [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
   
   ![image1][image1]
3. Za malou chvíli se vytvoří vaše aplikační služba. Projděte si různé nabídky a možnosti, které jsou v portálu pro službu dostupné. Dejte tomu několik minut.
   
   ![image2][image2]    
4. Klikněte na adresu URL. Všimněte si, že jsou pro nástroje a úložiště dostupné různé možnosti. Můžete taky použít jazyky a rozhraní, které chcete, včetně .NET, Java a Ruby.
   
   ![image3][image3]    
5. S portálem Azure je nasazování jednoduchý proces s několika málo kroky. Na portálu Azure zvolte nastavení z ikony pro app service, kterou jste vytvořili.
   
   ![image4][image4]
   
   Z okna, které se otevře na pravé straně, přejděte do části publikování.
   
   ![image5][image5]
6. Teď změníte některá nastavení a povolíte pro aplikaci průběžné nasazení. Klikněte na Zdroj nasazení a potom klikněte na tlačítko Vybrat zdroj. Všimněte si, že si můžete vybrat z různých úložišť zdroje.
   
   ![image6][image6]
7. V tomto příkladu vyberte Githubu. Volitelně můžete vybrat úložiště podle vašeho výběru a nastavit přihlašovací údaje pro autorizaci.
   
   ![image7][image7]
8. Po ověření přístupu úložiště pak můžete vybrat projekt a větev, kterou chcete nasadit. Dole je několik smyšlených příkladů.
   
   ![image8][image8]
9. Když si vyberete projekt a větev, klikněte na OK. Měla by se vám začít objevovat upozornění na nasazení.
   
   ![image9][image9]
10. Přejděte zpět na GitHub, kde uvidíte webhook vytvořený pro integraci úložiště správy zdrojového kódu s Azure. Portál Azure umožňuje integraci s Githubu se jenom pár jednoduchých kroků.
    
    ![image10][image10]
11. Abychom si ukázali průběžné nasazování, rychle do úložiště přidáte nějaký obsah. Snadné je třeba přidání ukázkového textového souboru do úložiště GitHub. Se službou App Service můžete použít aplikaci .NET, Ruby, Python nebo i jiný druh. Do vybraného úložiště můžete přidat textový soubor nebo aplikaci napsanou v ASP.NET MVC, Javě nebo Ruby.
    
    ![image11][image11]
12. Po potvrzení změn do úložiště uvidíte, že se v oblasti upozornění portálu oznámí zahájení nasazení. Pokud do několika chvil po odeslání do úložiště, neuvidíte změny, klikněte na Synchronizovat.
    
    ![image12][image12]
13. Pokud se teď pokusíte načíst stránku pro službu aplikace, může se zobrazit chyba 403. V tomto příkladu to nastalo, protože pro tuto stránku nejsou dostupná výchozí nastavení dokumentů, jako je třeba soubor index.htm nebo default.html. Můžete to rychle napravit pomocí nástrojů na portálu Azure.  Na portálu Azure vyberte nastavení &gt; nastavení aplikace.
    
     ![image13][image13]
14. Otevře se okno s nastavením aplikace. Zadejte název stránky „SamplePage.html“ a klikněte na Uložit. Podívejte se i na další nastavení, dejte tomu několik minut.
    
    ![image14][image14]
15. Pokud chcete, můžete v prohlížeči obnovit stránku a zkontrolovat, že se zobrazí očekávané změny. V tomto případě se teď na stránce zobrazí jednoduchý text. S každou další změnou v úložišti by se mělo automaticky provést nové nasazení.
    
    ![image15][image15]
    
    Povolení průběžné nasazování pomocí portálu Azure je snadný prostředí. Pro nasazení do Azure můžete vytvořit i složitější kanály pro vydávání a použít spoustu jiných postupů s existující správou zdrojového kódu a systémů průběžné integrace, jako je například využití automatizovaných systémů správy sestavení a vydávání.

## <a name="develop-and-test-an-app"></a>Vývoj a testování aplikace
Teď uděláme nějaké pár změn v základu kódu a tyto změny rychle nasadíme. Také nastavíte některé testování výkonu pro webové aplikace.

1. Na portálu Azure v navigačním podokně zvolte aplikační služby a vyhledejte App Service.
   
   ![image16][image16]
2. Klikněte na Nástroje
   
   ![image17][image17]
3. Všimněte si kategorie vývoje v části Nástroje. Existuje několik užitečné nástroje zde umožňujících nám pro práci s aplikací, aniž byste museli opustit portálu Azure. Klikněte na Konzolu.
   
   ![image18][image18]
4. V okně konzoly může v reálném čase zadávat příkazy pro svoji aplikaci. Zadejte příkaz dir a stiskněte enter. Všimněte si, že příkazy, které potřebují zvýšená oprávnění, nebudou fungovat.
   
   ![image19][image19]
5. Přejděte zpět do kategorie Vývoj a vyberte Visual Studio Online. Poznámka: Visual Studio Online je teď jmenuje Visual Studio Team Services.
   
   ![image20][image20]
6. Ve své aplikaci zapněte prostředí pro úpravy v prohlížeči.
   
   ![image21][image21]
7. Nainstaluje se webové rozšíření pro vaši aplikaci. Rozšíření můžou rychle a snadno přidat funkce do aplikací v Azure. Na snímku obrazovky dole si všimněte některých dalších dostupných typů rozšíření.
   
   ![image22][image22]
8. Až se rozšíření Visual Studio Online nainstaluje, klikněte na Přejít.
   
   ![image23][image23]
9. V prohlížeči se otevře karta, kde přímo v prohlížeči uvidíte integrované vývojové prostředí. Všimněte si, že dole je použitý prohlížeč Chrome.
   
   ![image24][image24]
10. Můžete provádět několik různých akcí, jako třeba upravit soubory, přidat soubory a složky a stahovat obsah z živého webu. Udělejte pár rychlých změn v souboru SamplePage.html.
    
    ![image25][image25]
11. Za chvíli se změny automaticky uloží. Když přejdete na zpět stránku, uvidíte změny. Nezapomeňte, že takovéto živé úpravy, nejspíš nejsou vhodné pro produkční prostředí. Tyto nástroje ale výrazně usnadňují provádění rychlé změn pro vývojové a testovací prostředí.
    
    ![image26][image26]
    
    ![image27][image27]
12. Přejděte zpět do okna nástrojů a v kategorii Vývoj klikněte na Test výkonnosti.
    
    ![image28][image28]
13. Musíte nastavit účet služby Team Services. Podrobnosti najdete tady: [Vytvoření účtu služby Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
14. Klikněte na Nový, tím vytvoříte test výkonnosti.
    
    ![image29][image29]
    
    Nakonfigurujte různé hodnoty a klikněte na Spustit Test v dolní části dialogu spuštění testu výkonnosti.
    
    ![image30][image30]
    
    ![image31][image31]
15. Když se test spustí, můžete sledovat stav.
    
    ![image32][image32]
    
    Když test skončí, klikněte na výsledek, tak zobrazíte další podrobnosti.
    
    ![image33][image33]
16. V tomto příkladu jste vytvořili malý testovací běh, takže k analýze máte jen omezené množství dat, ale můžete se podívat na různé metriky a z tohoto zobrazení test spustit znovu. Portál Azure díky vytváření, provádění a analýza výkonu webové testy jednoduchý proces. Dole na snímcích jsou vidět výkonnostní data.
    
    ![image34][image34]
    
    ![image35][image35]
    
    ![image36][image36]

## <a name="monitoring-and-troubleshooting-an-app"></a>Sledování a řešení potíží s aplikací
Azure poskytuje mnoho funkcí pro sledování a řešení potíží se spuštěnými aplikacemi.

1. Na portálu Azure pro webovou aplikaci příkaz nástroje.
   
   ![image37][image37]
2. V kategorii Řešení potíží si všimněte si různých možností pro řešení potíží s potenciální problémy se spuštěnými aplikacemi. Můžete třeba monitorovat živé přenosy HTTP, povolit samoopravování, zobrazit protokoly a další.
   
   ![image38][image38]
3. Vyberte Metriku lokality a uvidíte rychlé zobrazení některých kódů HTTP.
   
   ![image39][image39]
4. Vyberte Diagnostics as a Service. Vyberte typ své aplikace, pak klikněte na tlačítko Spustit.
   
   ![image40][image40]
   
   Začne sběr.
   
   ![image41][image41]
5. Můžete vybrat odpovídající protokol a diagnostikovat potenciální potíže. Abyste mohli vidět všechny možnosti pro data, jako je například Budete muset povolit protokolování zobrazíte všechny možnosti dostupných dat, jako například Protokoly HTTP.
   
   ![image42][image42]
   
   Kliknutím na soubor Výpis stavu paměti můžete stáhnout a analyzovat sestavu analýzy nástroje DebugDiag, která vám pomůže najít potenciální problémy.
   
   ![image43][image43]
6. Pokud chcete zobrazit víc dat, musíte povolit další protokolování. Na portálu Azure přejděte do webové aplikace a zvolte nastavení.
   
   ![image44][image44]
7. Přejděte dolů do kategorie funkcí a vyberte Diagnostické protokoly.
   
      ![image45][image45]
8. Všimněte si, že jsou různé možností pro protokolování. Zapněte protokolování webového serveru a klikněte na Uložit.
   
   ![image46][image46]
9. Přejděte zpět do oblasti nástroje pro aplikaci, vyberte Diagnostics as a service a klikněte na Spustit, tím znovu spustíte shromažďování dat.
   
   ![image47][image47]
10. Když je protokolování HTTP povolené, uvidíte data shromážděná pro protokoly HTTP.
    
    ![image48][image48]
11. Kliknutím na soubor protokolu HTML můžete vytvářet bohaté sestavy na bázi prohlížeče pro další zkoumání.
    
    ![image49][image49]
12. Zpět do části nástroje na portálu Azure pro aplikaci. Přejděte do části Nástroje a vyberte Process Explorer.
    
    ![image50][image50]
13. Když vyberete Process Explorer, můžete se podívat na podrobnosti o spuštěných procesech. Všimněte si níže můžete přejít k podrobnostem procesy a i ukončit procesy všechny z portálu Azure.
    
    ![image51][image51]
    
    ![image52][image52]
14. Přejděte zpět do levého okna nastavení. Klikněte na Novou žádost o podporu.
    
    ![image53][image53]
15. V pravém okně můžete zadat podrobnosti o problémech, kontaktní informace a dokonce odeslat diagnostická data. Portál Azure umožňuje práce s podporu společnosti Microsoft integrované prostředí.
    
    ![image54][image54]
    
    ![image55][image55]
    
    Portál Azure, pomáhá poskytovat výkonný a známých nástrojů prostředí, které vám pomůžou monitorování a řešení potíží s běžící aplikací. Můžete taky rychle provádět různé akce pomocí úloh, jako je například recyklace procesů, povolování a zakazování různých sběrů dat a dokonce integrace s profesionální podporou společnosti Microsoft.

## <a name="general-application-management"></a>Obecná správa aplikací
Při správě aplikací často potřebujete provádět množství různých činností, jako je například konfigurace strategií zálohování, implementace a správa poskytovatelů identit a konfigurace řízení přístupu na základě rolí. Stejně jiná prostředí DevOps i platforma Azure integruje tyto úlohy přímo do portálu.

1. Pokud chcete zajistit, že nemůže dojít ke ztrátě dat webové aplikace, musíte nastavit zálohování. Přejděte do oblasti Nastavení pro webovou aplikaci.
   
   ![image56][image56]
2. V pravém okně přejděte v kategorie Funkce.
   
    ![image57][image57]
3. Vyberte Zálohy. Na pravé straně se otevře okno.
   
   ![image58][image58]
4. Klikněte na Konfigurovat a v pravém okně vyberte účet úložiště.
   
   ![image59][image59]
5. Teď vytvořte a vyberte kontejner úložiště pro uložení záloh. Dole na formuláři klikněte na Vytvořit. Potom vyberte kontejner.
   
   ![image60][image60]
6. Po zvolení kontejneru, vám může konfigurovat plány, stejně jako nastavení zálohování pro své databáze. V tomto scénáři, klikněte na ikonu Uložit.
   
    ![image61][image61]
7. Po uložení přejděte zpět do levého okna pro Zálohy. Klikněte na Zálohovat a aplikace se zazálohuje.
   
    ![image62][image62]
8. Za chvíli uvidíte, že se vytvořila záloha. Na snímku obrazovky dole si všimněte možnosti Obnovit.
   
    ![image63][image63]
9. Klikněte na Obnovit a podívejte se na možnosti v pravém okně. Můžete vybrat zálohu, kterou chcete, a podle potřeby snadno obnovit předchozí stav. Portál Azure nám pomohl pro aplikaci jednoduše povolit strategie obnovení po havárii.
   
    ![image64][image64]
10. Přejděte zpět do levého okna nastavení a v části Funkce vyberte možnost Ověřování / autorizace.
    
     ![image65][image65]
11. V pravém okně vyberte Ověřování pomocí služby App Service. Všimněte si, že máte na výběr různé možnosti, které můžete konfigurovat pro oblíbené zprostředkovatele.
    
     ![image66][image66]
12. Vyberte poskytovatele, kterého chcete, a všimněte si možností pro obor. Můžete zadat ID aplikace a Tajný klíč aplikace a snadno pro aplikaci povolit ověření na Facebooku. Portál Azure umožňuje ověřování, jako to řešení na klíč pro aplikace.
    
     ![image67][image67]
13. Přejděte zpět do okna Nastavení a v kategorii Správa prostředků vyberte možnost Uživatelé.
    
     ![image68][image68]
14. V pravém okně se podívejte na různé možnosti pro přidávání rolí a uživatelů. Na portálu Azure můžete snadno nastavit RBAC (řízení přístupu na základě rolí) pro aplikaci.
    
     ![image69][image69]

## <a name="summary"></a>Souhrn
V tomto kurzu jsme vám ukázali některé schopnosti platformy Azure – rychlé zapnutí průběžného nasazování pro webovou aplikaci, provádění různých činností souvisejících s vývojem a testováním, sledování a řešení potíží s živou aplikací, a nakonec správu klíčových strategií, jako je zotavení po havárii, identita a řízení přístupu na základě rolí. Platforma Azure poskytuje integrované prostředí pro tyto pracovní postupy DevOps a vy můžete efektivně pracovat, protože budete mít přehled a všechny potřebné úkoly po ruce.

## <a name="next-steps"></a>Další postup
* Azure Resource Manager je důležitý k tomu, aby DevOps mohl fungovat na platformě Azure.  Další informace najdete v tématu [Přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md).
* Další informace o nasazování do Azure App Service najdete v tématu [Nasazení vaší aplikace do Azure App Service](../app-service/app-service-deploy-local-git.md)

[image1]: ./media/tutorial-azureportal-devops/image1.png
[image2]: ./media/tutorial-azureportal-devops/image2.png
[image3]: ./media/tutorial-azureportal-devops/image3.png
[image4]: ./media/tutorial-azureportal-devops/image4.png
[image5]: ./media/tutorial-azureportal-devops/image5.png
[image6]: ./media/tutorial-azureportal-devops/image6.png
[image7]: ./media/tutorial-azureportal-devops/image7.png
[image8]: ./media/tutorial-azureportal-devops/image8.png
[image9]: ./media/tutorial-azureportal-devops/image9.png
[image10]: ./media/tutorial-azureportal-devops/image10.png
[image11]: ./media/tutorial-azureportal-devops/image11.png
[image12]: ./media/tutorial-azureportal-devops/image12.png
[image13]: ./media/tutorial-azureportal-devops/image13.png
[image14]: ./media/tutorial-azureportal-devops/image14.png
[image15]: ./media/tutorial-azureportal-devops/image15.png
[image16]: ./media/tutorial-azureportal-devops/image16.png
[image17]: ./media/tutorial-azureportal-devops/image17.png
[image18]: ./media/tutorial-azureportal-devops/image18.png
[image19]: ./media/tutorial-azureportal-devops/image19.png
[image20]: ./media/tutorial-azureportal-devops/image20.png
[image21]: ./media/tutorial-azureportal-devops/image21.png
[image22]: ./media/tutorial-azureportal-devops/image22.png
[image23]: ./media/tutorial-azureportal-devops/image23.png
[image24]: ./media/tutorial-azureportal-devops/image24.png
[image25]: ./media/tutorial-azureportal-devops/image25.png
[image26]: ./media/tutorial-azureportal-devops/image26.png
[image27]: ./media/tutorial-azureportal-devops/image27.png
[image28]: ./media/tutorial-azureportal-devops/image28.png
[image29]: ./media/tutorial-azureportal-devops/image29.png
[image30]: ./media/tutorial-azureportal-devops/image30.png
[image31]: ./media/tutorial-azureportal-devops/image31.png
[image32]: ./media/tutorial-azureportal-devops/image32.png
[image33]: ./media/tutorial-azureportal-devops/image33.png
[image34]: ./media/tutorial-azureportal-devops/image34.png
[image35]: ./media/tutorial-azureportal-devops/image35.png
[image36]: ./media/tutorial-azureportal-devops/image36.png
[image37]: ./media/tutorial-azureportal-devops/image37.png
[image38]: ./media/tutorial-azureportal-devops/image38.png
[image39]: ./media/tutorial-azureportal-devops/image39.png
[image40]: ./media/tutorial-azureportal-devops/image40.png
[image41]: ./media/tutorial-azureportal-devops/image41.png
[image42]: ./media/tutorial-azureportal-devops/image42.png
[image43]: ./media/tutorial-azureportal-devops/image43.png
[image44]: ./media/tutorial-azureportal-devops/image44.png
[image45]: ./media/tutorial-azureportal-devops/image45.png
[image46]: ./media/tutorial-azureportal-devops/image46.png
[image47]: ./media/tutorial-azureportal-devops/image47.png
[image48]: ./media/tutorial-azureportal-devops/image48.png
[image49]: ./media/tutorial-azureportal-devops/image49.png
[image50]: ./media/tutorial-azureportal-devops/image50.png
[image51]: ./media/tutorial-azureportal-devops/image51.png
[image52]: ./media/tutorial-azureportal-devops/image52.png
[image53]: ./media/tutorial-azureportal-devops/image53.png
[image54]: ./media/tutorial-azureportal-devops/image54.png
[image55]: ./media/tutorial-azureportal-devops/image55.png
[image56]: ./media/tutorial-azureportal-devops/image56.png
[image57]: ./media/tutorial-azureportal-devops/image57.png
[image58]: ./media/tutorial-azureportal-devops/image58.png
[image59]: ./media/tutorial-azureportal-devops/image59.png
[image60]: ./media/tutorial-azureportal-devops/image60.png
[image61]: ./media/tutorial-azureportal-devops/image61.png
[image62]: ./media/tutorial-azureportal-devops/image62.png
[image63]: ./media/tutorial-azureportal-devops/image63.png
[image64]: ./media/tutorial-azureportal-devops/image64.png
[image65]: ./media/tutorial-azureportal-devops/image65.png
[image66]: ./media/tutorial-azureportal-devops/image66.png
[image67]: ./media/tutorial-azureportal-devops/image67.png
[image68]: ./media/tutorial-azureportal-devops/image68.png
[image69]: ./media/tutorial-azureportal-devops/image69.png
