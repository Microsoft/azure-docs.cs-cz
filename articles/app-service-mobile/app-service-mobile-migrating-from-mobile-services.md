---
title: Migrace z Mobile Services do mobilní aplikace služby App Service
description: Zjistěte, jak snadno migrujte své aplikace Mobile Services do mobilní aplikace služby App Service
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 07507ea2-690f-4f79-8776-3375e2adeb9e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: crdun
ms.openlocfilehash: dfc5e2923215b1669b0a3300653ad0cae7379655
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960730"
---
# <a name="article-top"></a>Migrace existující mobilní služby Azure do služby Azure App Service
S [Obecná dostupnost služby Azure App Service], weby, Azure Mobile Services můžete snadno migrovat místní využívat všechny funkce služby Azure App Service.  Tento dokument popisuje, co očekávat při migraci webu z Azure Mobile Services do služby Azure App Service.

## <a name="what-does-migration-do"></a>Co dělá migrace na web
Migrace mobilní služby Azure se změní na svojí mobilní služby do [Azure App Service] aplikace bez ovlivnění kódu.  Vaše Notification Hubs, SQL datové připojení, nastavení ověřování, naplánované úlohy a název domény zůstanou beze změny.  Mobilní klienti pomocí svojí mobilní služby Azure i nadále fungovat normálně.  Migrace restartuje vaše služba po přenosu do služby Azure App Service.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Proč byste migrovat webový server
Microsoft doporučuje, že migrujete svojí mobilní služby Azure, abyste mohli využívat výhody funkcí Azure App Service, včetně:

* Nové hostitelské funkce včetně [WebJobs] a [Vlastní názvy domén].
* Monitorování a řešení potíží s [Application Insights].
* Integrované nástroje DevOps, včetně [přípravné sloty], vrácení zpět a v produkčním prostředí testování.
* [Automatické škálování], Vyrovnávání zatížení, a [Sledování výkonu].

Další informace o výhodách služby Azure App Service, najdete v článku [Mobile Services versus App Service] tématu.

## <a name="before-you-begin"></a>Než začnete
Před zahájením jakékoli hlavní práce na webu, by měly zálohovat skripty mobilní služby a databáze SQL.

## <a name="migrating-site"></a>Migrace webů
Proces migrace migruje všechny lokality v rámci jedné oblasti Azure.

Chcete-li migrovat webový server:

1. Přihlaste se k [portál Azure Classic].
2. Vyberte mobilní službu v oblasti, kterou chcete migrovat.
3. Klikněte na tlačítko **migrovat do služby App Service** tlačítko.

   ![Tlačítko migrace][0]
4. Přečtěte si migrace na dialogovém okně App Service.
5. Zadejte název svojí mobilní služby do příslušného pole.  Například pokud je název domény contoso.azure-mobile.net, zadejte *contoso* v poli.
6. Klikněte na tlačítko značek.

Monitorování stavu migrace v nástroji Sledování aktivit. Váš web se zobrazí jako *migrace* na portálu Azure Classic.

  ![Monitorování aktivity migrace][1]

Každou migraci může trvat od 3 do 15 minut na mobilní službu migruje.  Váš web zůstávají dostupná během migrace.
Na konci procesu migrace se restartuje váš web.  Během procesu restartování může trvat několik sekund, není k dispozici webu.

## <a name="finalizing-migration"></a>Dokončení migrace
Naplánujte otestování webu z mobilního klienta při ukončení procesu migrace.  Ujistěte se, že můžete provádět všechny běžné akce klienta bez nutnosti změn mobilního klienta.  

### <a name="update-app-service-tier"></a>Vyberte cenovou úroveň příslušnou službu App Service
Máte větší flexibilitu v ceny po migraci na Azure App Service.

1. Přihlaste se k webu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název svojí přenesené mobilní služby.
3. Ve výchozím nastavení otevře se okno nastavení.
4. Klikněte na tlačítko **plán služby App Service** v nabídce nastavení.
5. Klikněte na tlačítko **cenová úroveň** dlaždici.
6. Kliknutím na dlaždici odpovídající vašim požadavkům a potom klikněte na **vyberte**.  Možná budete muset klikněte na tlačítko **zobrazit všechny** zobrazíte dostupné cenové úrovně.

Jako výchozí bod doporučujeme na následujících úrovních:

| Cenová úroveň mobilní služby | Cenová úroveň služby App Service |
|:--- |:--- |
| Free |F1 Free |
| Basic |B1 Basic |
| Standard |S1 Standard |

Existuje značnou flexibilitu při výběru doprava cenovou úroveň pro vaši aplikaci.  Odkazovat na [Ceny služeb App Service] úplné podrobnosti o cenách služby App Service.

> [!TIP]
> App Service úrovně Standard vrstva obsahuje přístup k mnoha funkcím, které chcete použít, včetně [přípravné sloty], automatické zálohování a automatické škálování.  Prohlédněte si nové funkce jsou existuje!
>
>

### <a name="review-migration-scheduler-jobs"></a>Zkontrolujte migrované Plánovač úloh
Úlohy scheduleru nebudou viditelné až po migraci přibližně 30 minut.  Naplánované úlohy se budou dál běžet pozadí.
Chcete-li zobrazit vaše naplánované úlohy poté, co jsou viditelné znovu:

1. Přihlaste se k webu [Azure Portal].
2. Vyberte **procházet >**, zadejte **plán** v *filtr* pole a pak vyberte **kolekce Scheduleru**.

Nejsou k dispozici omezený počet scheduler úrovně free úloh k dispozici po migraci.  Kontrola využití a [Plány v Azure Scheduleru].

### <a name="configure-cors"></a>Konfigurace CORS v případě potřeby
Sdílení prostředků mezi zdroji je technika umožňuje web tak, aby přístup k webovému rozhraní API v jiné doméně.  Pokud používáte Azure Mobile Services s přidruženým webem, budete muset nakonfigurovat CORS jako součást migrace.  Při přístupu k mobilním službám Azure výhradně z mobilních zařízení, potom CORS nemusí být nakonfigurovaná s výjimkou v ojedinělých případech.

Migrované nastavení CORS jsou k dispozici jako **MS_CrossDomainWhitelist** nastavení aplikace.  K migraci vašeho webu do zařízení CORS v App Service:

1. Přihlaste se k webu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název svojí přenesené mobilní služby.
3. Ve výchozím nastavení otevře se okno nastavení.
4. Klikněte na tlačítko **CORS** v nabídce rozhraní API.
5. Zadejte všechny povolené zdroje v poli, stisknutím klávesy Enter po každé z nich.
6. Až do seznamu Povolené zdroje je správná, klikněte na tlačítko Uložit.

> [!TIP]
> Jednou z výhod používání služby Azure App Service je ve stejné lokalitě můžete spustit web a mobilní služby.  Další informace najdete v tématu [další kroky](#next-steps) oddílu.
>
>

### <a name="download-publish-profile"></a>Stáhněte si nový profil publikování
Profil publikování webu se změní při migraci do Azure App Service.  Pokud máte v úmyslu publikovat na webu ze sady Visual Studio, budete potřebovat nový profil publikování.  Chcete-li stáhnout nový profil publikování:

1. Přihlaste se k webu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název svojí přenesené mobilní služby.
3. Klikněte na tlačítko **získat profil publikování**.

Soubor PublishSettings se stáhne do vašeho počítače.  Obvykle se nazývá *sitename*. PublishSettings.  Importujte nastavení publikování do existujícího projektu:

1. Otevřít Visual Studio a projekt mobilní služby Azure.
2. Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikování...**
3. Klikněte na **Importovat**.
4. Klikněte na tlačítko **Procházet** a vyberte vaše staženého souboru s nastavením publikování.  Klikněte na tlačítko **OK**.
5. Klikněte na tlačítko **ověřit připojení** zajistit pracovní nastavení publikování.
6. Klikněte na tlačítko **publikovat** publikování webu.

## <a name="working-with-your-site"></a>Práce s vaší lokality po migraci
Zahájení práce s vaší novou službu App Service v [Azure Portal] po migraci.  Tady jsou některé poznámky na určité operace, které jste použili k provádění v [portál Azure Classic]společně s jejich ekvivalentem služby App Service.

### <a name="publishing-your-site"></a>Stahování a publikování migrovaná lokalita
Váš web je k dispozici prostřednictvím git a ftp a můžete znovu publikovat s různými různé mechanismy, včetně nasazení webu, TFS, Mercurial, GitHub nebo FTP.  Přihlašovací údaje pro nasazení se migrují se zbytkem vaší lokality.  Pokud jste nenastavili přihlašovací údaje pro nasazení nebo si je nepamatujete, můžete je resetovat:

1. Přihlaste se k webu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název svojí přenesené mobilní služby.
3. Ve výchozím nastavení otevře se okno nastavení.
4. Klikněte na tlačítko **přihlašovací údaje pro nasazení** v publikování nabídky.
5. Zadejte nové přihlašovací údaje pro nasazení v příslušných polích a klikněte na tlačítko Uložit.

Tyto přihlašovací údaje můžete použít k lokalitě pomocí gitu naklonujte nebo nastavit automatizované nasazení z Githubu, TFS nebo Mercurial.  Další informace najdete v tématu [dokumentace pro nasazení služby Azure App Service].

### <a name="appsettings"></a>Nastavení aplikace
Většina nastavení pro migrované mobilních služeb jsou k dispozici prostřednictvím nastavení aplikace.  Můžete získat seznam nastavení aplikace z [Azure Portal].
Zobrazení nebo změna nastavení aplikace:

1. Přihlaste se k webu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název svojí přenesené mobilní služby.
3. Ve výchozím nastavení otevře se okno nastavení.
4. Klikněte na tlačítko **nastavení aplikace** v hlavní nabídce.
5. Přejděte do části Nastavení aplikace a najít nastavení aplikace.
6. Klikněte na hodnotu nastavení aplikace nastavte hodnotu upravit.  Klikněte na tlačítko **Uložit** uložte hodnotu.

Více nastavení aplikace můžete aktualizovat ve stejnou dobu.

> [!TIP]
> Existují dvě nastavení aplikace se stejnou hodnotou.  Například může zobrazit *vlastnosti ApplicationKey* a *MS\_vlastnosti ApplicationKey*.  Aktualizujte nastavení obě aplikace ve stejnou dobu.
>
>

### <a name="authentication"></a>Ověřování
Všechna nastavení ověřování jsou dostupné jako nastavení aplikace v migrovaná lokalita.  Pokud chcete aktualizovat nastavení ověřování, je nutné změnit nastavení příslušné aplikace.  Následující tabulka uvádí nastavení odpovídající aplikace pro vaše zprostředkovatele ověřování:

| Poskytovatel | ID klienta | Tajný kód klienta | Další nastavení |
|:--- |:--- |:--- |:--- |
| Účet Microsoft |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

Poznámka: **MS\_AadTenants** se ukládá jako čárkami oddělený seznam domén tenanta (pole "Povolené Tenantů" na portálu Mobile Services).

> [!WARNING]
> **V nabídce nastavení nepoužívají mechanismy ověřování**
>
> Azure App Service poskytuje samostatného "bez kódu" ověřování a autorizace systému v rámci *ověřování / autorizace* nabídky nastavení a (zastaralé) *ověřování mobilní* možnost v nabídce nastavení.  Tyto možnosti jsou kompatibilní s migrované mobilní službu Azure.  Je možné [upgradu lokality](app-service-mobile-net-upgrading-from-mobile-services.md) využívat ověřování služby Azure App Service.
>
>

### <a name="easytables"></a>Data
*Data* kartu v Mobile Services se nahradil *jednoduché tabulky* na webu Azure portal.  Pro přístup k jednoduché tabulky:

1. Přihlaste se k webu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název svojí přenesené mobilní služby.
3. Ve výchozím nastavení otevře se okno nastavení.
4. Klikněte na tlačítko **jednoduché tabulky** v mobilní nabídce.

Tabulku můžete přidat kliknutím **přidat** tlačítko nebo přístup k vaší existující tabulky kliknutím na název tabulky.  Existují různé operace lze provádět z tohoto okna, včetně:

* Změna oprávnění tabulky
* Úpravy provozní skripty
* Správa schématu tabulky
* Odstraňuje se tabulka
* Vymazání obsahu tabulky
* Odstraňuje se konkrétní řádky v tabulce

### <a name="easyapis"></a>API
*API* kartu v Mobile Services se nahradil *jednoduchá rozhraní API* na webu Azure portal.  Pro přístup k jednoduchá rozhraní API:

1. Přihlaste se k webu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název svojí přenesené mobilní služby.
3. Ve výchozím nastavení otevře se okno nastavení.
4. Klikněte na tlačítko **jednoduchá rozhraní API** v mobilní nabídce.

Migrované rozhraní API jsou už uvedené v okně.  Z tohoto okna můžete také přidat rozhraní API.  Ke správě konkrétního rozhraní API, klikněte na rozhraní API.
V okně Nový můžete upravit oprávnění a upravit skripty pro rozhraní API.

### <a name="on-demand-jobs"></a>Úlohy scheduleru
Všechny plánovače úloh jsou k dispozici prostřednictvím oddílu kolekce úloh Scheduleru.  Pro přístup k vaší úlohy scheduleru:

1. Přihlaste se k webu [Azure Portal].
2. Vyberte **procházet >**, zadejte **plán** v *filtr* pole a pak vyberte **kolekce Scheduleru**.
3. Vyberte kolekci úloh pro váš web.  Je název *sitename*-úlohy.
4. Klikněte na tlačítko **nastavení**.
5. Klikněte na tlačítko **úlohy Scheduleru** v části Správa.

Naplánované úlohy jsou uvedeny s frekvencí, který jste zadali před migrací.  Úlohy na vyžádání jsou zakázané.  Pokud chcete spustit úlohu služby na vyžádání:

1. Vyberte úlohu, kterou chcete spustit.
2. V případě potřeby klikněte na tlačítko **povolit** povolit úlohy.
3. Klikněte na tlačítko **nastavení**, pak **plán**.
4. Vyberte opakování **jednou**, pak klikněte na tlačítko **uložit**

Úlohy na vyžádání se nacházejí v `App_Data/config/scripts/scheduler post-migration`.  Doporučujeme, abyste převedli všechny úlohy na vyžádání [WebJobs] nebo [Functions].  Zápis nové úlohy scheduleru jako [WebJobs] nebo [Functions].

### <a name="notification-hubs"></a>Notification Hubs
Mobile Services pomocí Notification Hubs pro nabízená oznámení.  Následující nastavení aplikace se používají k propojení centra oznámení do svojí mobilní služby po migraci:

| Nastavení aplikace | Popis |
|:--- |:--- |
| **MS\_PushEntityNamespace** |Namespace centra oznámení |
| **MS\_NotificationHubName** |Název centra oznámení |
| **MS\_NotificationHubConnectionString** |Připojovací řetězec centra oznámení |
| **MS\_NamespaceName** |Alias pro MS_PushEntityNamespace |

Vaše centrum oznámení je spravována prostřednictvím [Azure Portal].  Poznamenejte si název centra oznámení (zjistíte ji pomocí nastavení aplikace):

1. Přihlaste se k webu [Azure Portal].
2. Vyberte **Procházet**> a pak vyberte **Notification Hubs**
3. Klikněte na název centra oznámení přidružený k mobilní službě.

> [!NOTE]
> Pokud vaše Centrum oznámení je typem "Různé", není viditelný.  "Hybridní" typ oznámení, že hubs využívat Notification Hubs a funkce služby starší verze služby Service Bus.  [Převést smíšené obory názvů] než budete pokračovat.  Po dokončení převodu, se zobrazí v centru oznámení [Azure Portal].
>
>

Další informace najdete v článku [Notification Hubs] dokumentaci.

> [!TIP]
> Funkce správy Notification Hubs v [Azure Portal] jsou stále ve verzi preview.  [Portál Azure Classic] zůstává k dispozici pro všemi Notification Hubs pro správu.
>
>

### <a name="legacy-push"></a>Nastavení služby Push. starší verze
Pokud jste nakonfigurovali nabízených oznámení na mobilní službu před zavedením do Notification Hubs, kterou používáte *starší verze nabízených*.  Pokud používáte nabízenou a se nezobrazí na centrum oznámení, které jsou uvedené ve vaší konfiguraci a potom je pravděpodobné, že používáte *starší verze nabízených*.  Tato funkce je migrovat všechny ostatní funkce.  Doporučujeme však, že upgradujete na Notification Hubs, brzy po dokončení migrace.

Prozatím všechna nastavení nabízených starší verze (s výjimkou významné server certifikát služby APN) jsou k dispozici v nastavení aplikace.  Aktualizujte certifikát služby APN nahrazením příslušného souboru v systému souborů.

### <a name="app-settings"></a>Další nastavení aplikace
Následující nastavení další aplikace jsou migrovat ze svojí mobilní služby a budou dostupné v rámci *nastavení* > *nastavení aplikace*:

| Nastavení aplikace | Popis |
|:--- |:--- |
| **MS\_MobileServiceName** |Název vaší aplikace |
| **MS\_MobileServiceDomainSuffix** |Předpona domény. i.e azure-mobile.net |
| **MS\_ApplicationKey** |Klíč aplikace |
| **MS\_MasterKey** |Hlavní klíč vaší aplikace |

Klíč aplikace a hlavní klíč jsou stejné jako klíče aplikace z původního mobilních službách.  Konkrétně se klíč aplikace posílá mobilní klienty ověřit jejich používání mobilní rozhraní API.

### <a name="cliequivalents"></a>Ekvivalenty příkazového řádku
Už vám *azure mobilních* příkazu spravujte svůj web Azure Mobile Services.  Místo toho mnoho funkcí se nahradily *azure lokality* příkazu.  Použijte následující tabulku ekvivalenty pro běžné příkazy:

| *Azure Mobile* příkaz | Ekvivalentní *lokality Azure* příkaz |
|:--- |:--- |
| mobilní umístění |umístění seznamu webů |
| mobilní seznamu |seznam webů |
| Zobrazit mobilní *název* |Zobrazit lokality *název* |
| mobilní restartování *název* |restartování webu *název* |
| Opětovné nasazení mobilní *název* |Opětovné nasazení v rámci nasazení serveru *commitId* *název* |
| mobilní sady klíčů *název* *typ* *hodnota* |Odstranit nastavení aplikace webu *klíč* *název* <br/> Přidat nastavení aplikace webu *klíč*=*hodnotu* *název* |
| Seznam mobilních config *název* |seznam nastavení aplikace webu *název* |
| získat mobilní konfigurace *název* *klíč* |Zobrazit nastavení aplikace webu *klíč* *název* |
| mobilní konfigurační sada *název* *klíč* |Odstranit nastavení aplikace webu *klíč* *název* <br/> Přidat nastavení aplikace webu *klíč*=*hodnotu* *název* |
| Seznam mobilních domén *název* |seznam domén lokality *název* |
| Přidat mobilní domény *název* *domény* |Přidat doménu webu *domény* *název* |
| Odstranění mobilních domény *název* |odstranění domény webu *domény* *název* |
| Zobrazit mobilní škálování *název* |Zobrazit lokality *název* |
| změnit měřítko mobilní *název* |režim škálování webu *režimu* *název* <br /> lokality škálovaných instancí *instance* *název* |
| seznam nastavení mobilní aplikace *název* |seznam nastavení aplikace webu *název* |
| nastavení mobilní aplikace přidat *název* *klíč* *hodnota* |Přidat nastavení aplikace webu *klíč*=*hodnotu* *název* |
| Odstranit nastavení mobilní aplikace *název* *klíč* |Odstranit nastavení aplikace webu *klíč* *název* |
| Zobrazit nastavení mobilní aplikace *název* *klíč* |Odstranit nastavení aplikace webu *klíč* *název* |

Aktualizovat ověřování nebo nastavení služby push notification aktualizuje příslušná nastavení aplikace.
Úpravy souborů a publikování webu přes protokol ftp nebo git.

### <a name="diagnostics"></a>Protokolování a Diagnostika
Protokolování diagnostiky se běžně zakázané ve službě Azure App Service.  Pokud chcete povolit protokolování diagnostiky:

1. Přihlaste se k webu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název svojí přenesené mobilní služby.
3. Ve výchozím nastavení otevře se okno nastavení.
4. Vyberte **diagnostické protokoly** nabídce funkcí.
5. Klikněte na tlačítko **ON** pro tyto protokoly: **Protokolování aplikace (systém souborů)**, **podrobné chybové zprávy**, a **chybných požadavků**
6. Klikněte na tlačítko **systému souborů** pro protokolování webového serveru
7. Klikněte na **Uložit**.

K zobrazení protokolů:

1. Přihlaste se k webu [Azure Portal].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název svojí přenesené mobilní služby.
3. Klikněte na tlačítko **nástroje** tlačítko
4. Vyberte **Stream protokolů** nabídce dodržovat.

Protokoly se zobrazují v okně, jako jsou generovány.  Můžete také stáhnout protokoly pro pozdější analýzu pomocí přihlašovacích údajů pro nasazení. Další informace najdete v tématu [Logging] dokumentaci.

## <a name="known-issues"></a>Známé problémy
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Odstranění klon migrovat mobilní aplikace způsobí, že výpadku lokality
Pokud klonovat migrované mobilní služby pomocí Azure Powershellu a pak odstraňte klonování, odeberou se záznam DNS pro služby v produkčním prostředí.  Váš web se už nebude přístupný z Internetu.  

Řešení: Pokud chcete klonovat vašeho webu, udělejte na portálu.

### <a name="changing-webconfig-does-not-work"></a>Změna souboru Web.config nefunguje
Pokud máte webová aplikace ASP.NET, se změní na `Web.config` souboru není použije.  Azure App Service vytvoří vhodný `Web.config` souboru při spuštění pro podporu modulu runtime Mobile Services.  Určitá nastavení (například vlastní záhlaví) můžete přepsat pomocí transformačnímu souboru XML.  Vytvořte soubor kromě volá `applicationHost.xdt` – tento soubor musí končit `D:\home\site` ve službě Azure.  Nahrát `applicationHost.xdt` souboru prostřednictvím skriptu vlastní nasazení nebo přímo pomocí Kudu.  Následuje ukázkový dokument:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Další informace najdete v tématu [Ukázky XDT transformace] dokumentaci na Githubu.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Migrované služby Mobile se nedají přidat do Traffic Manageru
Při vytváření profilu Traffic Manageru nelze přímo vybrat migrované mobilní službě pro profil.  Použití "externí koncový bod."  Externí koncový bod je možné přidat pouze prostřednictvím prostředí PowerShell.  Další informace najdete v tématu [Traffic Manageru kurzu](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Další kroky
Teď, když vaše aplikace je migrovat do služby App Service, existují ještě další funkce, které můžete použít:

* Nasazení [přípravné sloty] umožňují změny do vaší lokality a provádění A / B testování.
* [WebJobs] nenabízí žádnou náhradu za úlohy naplánované na vyžádání.
* Je možné [průběžné nasazování] webu propojením vašeho webu na GitHub, TFS nebo Mercurial.
* Můžete použít [Application Insights] k monitorování vašeho webu.
* Poskytování webu a mobilních API z stejný kód.

### <a name="upgrading-your-site"></a>Upgrade webu služby Mobile Services k Azure Mobile Apps SDK
* Pro projekty založené na Node.js serveru nové [Mobile Apps Node.js SDK] nabízí několik nových funkcí. Například můžete nyní provést místní vývoj a ladění, použít libovolnou verzi Node.js nad 0.10 a přizpůsobit se veškerý middleware Express.js.
* Pro. Projekty serveru na základě NET, nové [balíčky NuGet sady SDK pro Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) máte větší flexibilitu v závislosti na NuGet.  Tyto balíčky podporu ověřování nové služby App Service a compose s žádným projektem ASP.NET. Další informace o upgradu najdete v tématu [Upgrade existující mobilní služby .NET do služby App Service](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Ceny služeb App Service]: https://azure.microsoft.com/pricing/details/app-service/
[Application Insights]: ../azure-monitor/app/app-insights-overview.md
[Automatické škálování]: ../app-service/web-sites-scale.md
[Azure App Service]: ../app-service/overview.md
[portál Azure Classic]: https://manage.windowsazure.com
[Azure Portal]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/regions/
[Plány v Azure Scheduleru]: ../scheduler/scheduler-plans-billing.md
[průběžné nasazování]: ../app-service/deploy-continuous-deployment.md
[Převést smíšené obory názvů]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: https://curl.haxx.se/
[Vlastní názvy domén]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: https://www.telerik.com/fiddler
[Obecná dostupnost služby Azure App Service]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[Logging]: ../app-service/troubleshoot-diagnostic-logs.md
[Mobile Apps Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[Mobile Services versus App Service]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notification Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Sledování výkonu]: ../app-service/web-sites-monitor.md
[Postman]: https://www.getpostman.com/
[přípravné sloty]: ../app-service/deploy-staging-slots.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[Ukázky XDT transformace]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Functions]: ../azure-functions/functions-overview.md
