---
title: Migrace z mobilních služeb do mobilní aplikace služby App Service
description: Zjistěte, jak snadno migrovat aplikace Mobile Services pro aplikaci služby mobilní aplikace
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
ms.openlocfilehash: 5001704f47af0c7b07744f1dceb7aa58bdb6448c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="article-top"></a>Migrovat existující Mobile Service Azure do Azure App Service
Pomocí [obecné dostupnosti služby Azure App Service], weby Azure Mobile Services můžete snadno migrovat na místě chcete využít výhod všech funkcí služby Azure App Service.  Tento dokument popisuje, co očekávat při migraci váš web z Azure Mobile Services do služby Azure App Service.

## <a name="what-does-migration-do"></a>Co dělá migrace na váš web
Migrace služby Azure Mobile změní služby Mobile do [Azure App Service] aplikace, aniž by to ovlivnilo kód.  Vaše centra oznámení, SQL datové připojení, nastavení ověřování, naplánované úlohy a název domény zůstanou beze změny.  Mobilních klientů pomocí služby Azure Mobile i nadále fungovat normálně.  Migrace služby restartuje, jakmile se přenese do služby Azure App Service.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Proč by měl migrovat webový server
Microsoft doporučuje, který migrujete služby Azure Mobile chcete využít výhod funkce Azure App Service, včetně:

* Nové hostitelské funkce včetně [WebJobs] a [vlastních názvů domén].
* Monitorování a řešení potíží s [Application Insights].
* Předdefinované DevOps nástrojů, včetně [přípravné sloty], vrácení zpět a v produkční testování.
* [Automatické škálování], Vyrovnávání zatížení a [monitorování výkonu].

Další informace o výhodách Azure App Service naleznete v tématu [vs Mobile Services. Služby App Service] tématu.

## <a name="before-you-begin"></a>Než začnete
Před zahájením všechna hlavní práce na svém webu, měli zálohovat skripty mobilní služby a databáze SQL.

## <a name="migrating-site"></a>Migrace vaší lokality
V procesu migrace migruje všechny lokality v jedné oblasti Azure.

Chcete-li migrovat webový server:

1. Přihlaste se k [portálu Azure Classic].
2. Vyberte mobilní službu v oblast, kterou chcete migrovat.
3. Klikněte **migrací do služby App Service** tlačítko.

   ![Tlačítko migrací][0]
4. Přečtěte si migrace na dialogovém okně App Service.
5. Zadejte název vaší služby Mobile v poli.  Například pokud je název vaší domény contoso.azure mobile.net, zadejte *contoso* v poli.
6. Klikněte na tlačítko značek.

Monitorování stavu migrace v monitoru aktivity. Váš web je uveden jako *migrace* na portálu Azure Classic.

  ![Monitorování aktivity migrace][1]

Každý migrace trvat od 3 do 15 minut za mobilní služby se migruje.  Váš web zůstává k dispozici během migrace.
Na konci procesu migrace se restartuje vaší lokality.  Web není k dispozici během procesu restartování, které mohou trvat několik sekund.

## <a name="finalizing-migration"></a>Dokončení migrace
Naplánujte otestování váš web z mobilního klienta při ukončení procesu migrace.  Zkontrolujte, zda že je možné provádět všechny běžné akce klienta bez nutnosti změn mobilního klienta.  

### <a name="update-app-service-tier"></a>Vyberte odpovídající aplikační službu cenové úrovně
Máte větší flexibilitu v po migraci na Azure App Service – ceny.

1. Přihlaste se k portálu [portál Azure].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší migrované mobilní služby.
3. Otevře se okno nastavení ve výchozím nastavení.
4. Klikněte na tlačítko **plán služby App Service** v nabídce nastavení.
5. Klikněte **cenová úroveň** dlaždici.
6. Klikněte na dlaždici vhodné pro vaše požadavky a pak klikněte na **vyberte**.  Budete muset klikněte na tlačítko **zobrazit všechny** zobrazíte dostupné cenové úrovně.

Jako počáteční bod doporučujeme, abyste na následujících úrovních:

| Cenová úroveň mobilní služby | Cenová úroveň služby App Service |
|:--- |:--- |
| Free |F1 Free |
| Basic |B1 Basic |
| Standard |S1 Standard |

Není značnou flexibilitu při volbě právo cenovou úroveň pro vaši aplikaci.  Odkazovat na [App Service – ceny] úplné podrobnosti o cenách služby App Service.

> [!TIP]
> Aplikace služby standardní vrstvě obsahuje přístup k řadu funkcí, které chcete použít, včetně [přípravné sloty], automatické zálohování a automatické škálování.  Podívejte se na nové funkce, když jste existuje!
>
>

### <a name="review-migration-scheduler-jobs"></a>Zkontrolujte migrované Plánovač úloh
Plánovač úloh se nezobrazí dokud přibližně 30 minut po migraci.  Naplánované úlohy dál spuštěný na pozadí.
Chcete-li zobrazit naplánované úlohy, jakmile jsou viditelné znovu:

1. Přihlaste se k portálu [portál Azure].
2. Vyberte **procházet >**, zadejte **plán** v *filtru* pole a pak vyberte **kolekce plánovače**.

Existují omezení počtu volné plánovače úloh k dispozici po migraci.  Zkontrolujte vaše využití a [Azure Scheduler plánuje].

### <a name="configure-cors"></a>Konfigurace CORS v případě potřeby
Sdílení prostředků různého původu je technika umožňuje webu pro přístup k webové rozhraní API v jiné doméně.  Pokud používáte Azure Mobile Services s přidruženým webem, budete muset nakonfigurovat jako součást migrace CORS.  Pokud pracujete s Azure Mobile Services výhradně z mobilních zařízení, potom CORS nemusí být nakonfigurovaná s výjimkou ve výjimečných případech.

Jsou k dispozici jako migrované nastavení CORS **MS_CrossDomainWhitelist** nastavení aplikace.  Chcete-li migrovat webový server do zařízení CORS služby App Service:

1. Přihlaste se k portálu [portál Azure].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší migrované mobilní služby.
3. Otevře se okno nastavení ve výchozím nastavení.
4. Klikněte na tlačítko **CORS** v nabídce rozhraní API.
5. V poli, stisknutím klávesy Enter po každé z nich zadejte žádné povolené zdroje.
6. Jakmile seznam Povolené zdroje je správná, klikněte na tlačítko Uložit.

> [!TIP]
> Jednou z výhod používání Azure App Service je na stejném webu můžete spustit web a mobilní služby.  Další informace najdete v tématu [další kroky](#next-steps) části.
>
>

### <a name="download-publish-profile"></a>Stáhněte si nový profil publikování
Profil publikování webu se změní při migraci do služby Azure App Service.  Pokud chcete publikovat váš web z Visual Studia, je třeba nový profil publikování.  Chcete-li stáhnout nový profil publikování:

1. Přihlaste se k portálu [portál Azure].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší migrované mobilní služby.
3. Klikněte na tlačítko **profilu publikování Get**.

Soubor PublishSettings se stáhne do počítače.  Obvykle se označuje jako *sitename*. PublishSettings.  Importujte nastavení publikování do vašeho existujícího projektu:

1. Otevřete Visual Studio a projekt mobilní služby Azure.
2. Klikněte pravým tlačítkem na projekt v **Průzkumníku řešení** a vyberte **publikování...**
3. Klikněte na **Importovat**.
4. Klikněte na tlačítko **Procházet** a vyberte vaše stažený soubor nastavení publikování.  Klikněte na tlačítko **OK**.
5. Klikněte na tlačítko **ověřit připojení** aby fungoval nastavení publikování.
6. Klikněte na tlačítko **publikovat** k publikování webu.

## <a name="working-with-your-site"></a>Práce s vaší lokality po migraci
Zahájení práce s vaší nové služby App Service v [portál Azure] po migraci.  Toto jsou některé poznámky na konkrétní operace, které jste použili k plnění [portálu Azure Classic], společně s jejich ekvivalent služby App Service.

### <a name="publishing-your-site"></a>Stahování a publikování migrovaná lokalita
Váš web je k dispozici prostřednictvím git a ftp a můžete publikovat různé různé mechanismy, včetně WebDeploy, sady TFS, Mercurial, Githubu a FTP.  Přihlašovací údaje nasazení se migrují se zbytkem vaší lokality.  Pokud jste nenastavili přihlašovací údaje nasazení, nebo si je nepamatujete, můžete je obnovit:

1. Přihlaste se k portálu [portál Azure].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší migrované mobilní služby.
3. Otevře se okno nastavení ve výchozím nastavení.
4. Klikněte na tlačítko **přihlašovací údaje nasazení** v publikování nabídky.
5. Zadejte nová pověření pro nasazení do příslušných polí a potom klikněte na tlačítko Uložit.

Tyto přihlašovací údaje můžete použít k lokalitě pomocí git clone nebo nastavit automatické nasazení z Githubu, sady TFS nebo Mercurial.  Další informace najdete v dokumentaci [Azure App Service nasazení].

### <a name="appsettings"></a>Nastavení aplikace
Většina nastavení migrovaných mobilní služby jsou dostupné prostřednictvím nastavení aplikace.  Můžete získat seznam nastavení aplikace z [portál Azure].
Zobrazení nebo změna nastavení aplikace:

1. Přihlaste se k portálu [portál Azure].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší migrované mobilní služby.
3. Otevře se okno nastavení ve výchozím nastavení.
4. Klikněte na tlačítko **nastavení aplikace** v hlavní nabídce.
5. Přejděte do části Nastavení aplikace a najít nastavení vaší aplikace.
6. Klikněte na hodnotu nastavení aplikace a příslušnou hodnotu upravte.  Klikněte na tlačítko **Uložit** uložte hodnotu.

Současně můžete aktualizovat více nastavení aplikace.

> [!TIP]
> Existují dvě nastavení aplikace se stejnou hodnotou.  Například, mohou se zobrazit *ApplicationKey* a *MS\_ApplicationKey*.  Aktualizujte nastavení obě aplikace ve stejnou dobu.
>
>

### <a name="authentication"></a>Ověřování
Všechna nastavení ověřování jsou k dispozici jako nastavení aplikace v migrovaná lokalita.  Pokud chcete aktualizovat svoje nastavení ověřování, je nutné změnit nastavení příslušné aplikace.  Následující tabulka uvádí nastavení příslušné aplikace pro zprostředkovatele ověřování:

| Poskytovatel | ID klienta | Tajný klíč klienta | Další nastavení |
|:--- |:--- |:--- |:--- |
| Účet Microsoft |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

Poznámka: **MS\_AadTenants** se ukládají jako textový soubor s oddělovači seznam domén klienta (pole "Klientům povoleno" na portálu Mobile Services).

> [!WARNING]
> **V nabídce nastavení nepoužívejte mechanismy ověřování**
>
> Aplikační služba Azure poskytuje samostatném "bez použití kódu" ověřování a autorizace systému v rámci *ověřování / autorizace* nabídky nastavení a (zastaralé) *ověřování mobilní* možnost v nabídce nastavení.  Tyto možnosti jsou kompatibilní s migrované mobilní služby Azure.  Můžete [upgradu lokality](app-service-mobile-net-upgrading-from-mobile-services.md) využívat výhod ověřování služby Azure App Service.
>
>

### <a name="easytables"></a>data
*Data* kartě v Mobile Services nahradila *snadno tabulky* v rámci portálu Azure.  Pro přístup k snadno tabulky:

1. Přihlaste se k portálu [portál Azure].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší migrované mobilní služby.
3. Otevře se okno nastavení ve výchozím nastavení.
4. Klikněte na tlačítko **snadno tabulky** v nabídce mobilních.

Kliknutím můžete přidat tabulku **přidat** tlačítko nebo přístup k vaší stávající tabulky kliknutím na název tabulky.  Existují různé operace, které můžete provést z tohoto okna, včetně:

* Změna oprávnění tabulky
* Úpravy provozní skripty
* Správa schématu tabulky
* Odstraňování tabulky
* Vymazání obsahu tabulky
* Odstranění konkrétní řádků tabulky

### <a name="easyapis"></a>ROZHRANÍ API
*Rozhraní API* kartě v Mobile Services nahradila *rozhraní API pro snadný* v rámci portálu Azure.  Pro přístup k rozhraní API pro snadný:

1. Přihlaste se k portálu [portál Azure].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší migrované mobilní služby.
3. Otevře se okno nastavení ve výchozím nastavení.
4. Klikněte na tlačítko **rozhraní API pro snadný** v nabídce mobilních.

Vaše migrované rozhraní API jsou již uveden v okně.  V tomto okně můžete také přidat rozhraní API.  Chcete-li spravovat konkrétní rozhraní API, klikněte na rozhraní API.
V okně nové můžete upravit oprávnění a upravit skripty pro rozhraní API.

### <a name="on-demand-jobs"></a>Plánovač úloh
Všechny plánovače úloh jsou k dispozici prostřednictvím části kolekce úloh plánovače.  Pro přístup k vaší plánovače úloh:

1. Přihlaste se k portálu [portál Azure].
2. Vyberte **procházet >**, zadejte **plán** v *filtru* pole a pak vyberte **kolekce plánovače**.
3. Vyberte kolekci úloh pro svůj web.  Je název *sitename*-úlohy.
4. Klikněte na tlačítko **nastavení**.
5. Klikněte na tlačítko **Plánovač úloh** v části Správa.

Naplánované úlohy jsou uvedeny s četností, které zadáte před migrací.  Úlohy na vyžádání jsou zakázány.  Spustit úlohu na vyžádání:

1. Vyberte úkol, který chcete spustit.
2. V případě potřeby klikněte na tlačítko **povolit** povolit úlohu.
3. Klikněte na tlačítko **nastavení**, pak **plán**.
4. Vyberte opakování **jednou**, pak klikněte na tlačítko **uložit**

Vaše úlohy na vyžádání se nacházejí v `App_Data/config/scripts/scheduler post-migration`.  Doporučujeme vám, že převedete všechny úlohy na vyžádání [WebJobs] nebo [funkce].  Zápis nových úloh plánovače jako [WebJobs] nebo [funkce].

### <a name="notification-hubs"></a>Centra oznámení
Mobile Services používá centra oznámení pro nabízená oznámení.  Následující nastavení aplikace se používají k propojení centra oznámení k mobilní službě po migraci:

| Nastavení aplikace | Popis |
|:--- |:--- |
| **MS\_PushEntityNamespace** |Namespace centra oznámení |
| **MS\_NotificationHubName** |Název centra oznámení. |
| **MS\_NotificationHubConnectionString** |Připojovací řetězec centra oznámení |
| **MS\_NamespaceName** |Alias pro MS_PushEntityNamespace |

Vaše centrum oznámení je spravovat prostřednictvím [portál Azure].  Poznamenejte si název centra oznámení (můžete najít to pomocí nastavení aplikace):

1. Přihlaste se k portálu [portál Azure].
2. Vyberte **Procházet**>, pak vyberte **centra oznámení**
3. Klikněte na název centra oznámení, který je přidružený k mobilní službě.

> [!NOTE]
> Pokud vaše Centrum oznámení je typu "Mixed", není viditelná.  "Smíšený" typ oznámení, že hubs využívat Notification Hubs a starší verze funkce Service Bus.  [Převést smíšený obory] než budete pokračovat.  Po dokončení převodu, se zobrazí v centru oznámení [portál Azure].
>
>

Další informace najdete v článku [Notification Hubs] dokumentaci.

> [!TIP]
> Funkce správy centra oznámení [portál Azure] jsou stále ve verzi preview.  [portálu Azure Classic] zůstává k dispozici pro správu všech Notification Hubs.
>
>

### <a name="legacy-push"></a>Starší verze nabízené nastavení
Pokud jste nakonfigurovali nabízené na mobilní službu před zavedením na centra oznámení, že používáte *starší verze nabízené*.  Pokud používáte nabízenou a nevidíte centra oznámení uvedené v konfiguraci, pak je pravděpodobné, že používáte *starší verze nabízené*.  Tato funkce je migrována se všechny ostatní funkce.  Doporučujeme však brzy po dokončení migrace upgradu na centra oznámení.

Během provádění změn nastavení starší verze nabízené (s výjimkou významné certifikátu služby APN) jsou k dispozici v nastavení aplikace.  Aktualizujte certifikát služby APN nahrazením příslušný soubor na systém souborů.

### <a name="app-settings"></a>Další nastavení aplikace
Následující nastavení dalších aplikací jsou migrované z mobilní služby a k dispozici v části *nastavení* > *nastavení aplikace*:

| Nastavení aplikace | Popis |
|:--- |:--- |
| **MS\_MobileServiceName** |Název vaší aplikace |
| **MS\_MobileServiceDomainSuffix** |Předpona domény. jednofaktorovému azure-mobile.net |
| **MS\_ApplicationKey** |Klíč vaší aplikace |
| **MS\_MasterKey** |Hlavní klíč vaší aplikace |

Klíč aplikace a hlavní klíč jsou identické s klíči aplikací z původního mobilní služby.  Klíč aplikace je odeslán na konkrétní mobilní klienty pro ověření jejich používání mobilní rozhraní API.

### <a name="cliequivalents"></a>Ekvivalenty příkazového řádku
Už můžete použít *azure mobilní* příkaz ke správě vašeho webu Azure Mobile Services.  Místo toho mnoho funkcí nahradil *azure lokality* příkaz.  Pomocí následující tabulky ekvivalenty pro běžné příkazy:

| *Azure Mobile* příkaz | Ekvivalentní *Azure Site* příkaz |
|:--- |:--- |
| mobilní umístění |Seznam umístění lokality |
| mobilní seznamu |seznam webů |
| mobilní zobrazit *název* |Zobrazit lokality *název* |
| mobilní restartování *název* |lokality restartování *název* |
| mobilní znovu ho zaveďte *název* |lokality nasazení znovu ho zaveďte *commitId* *název* |
| mobilní sady klíčů *název* *typ* *hodnota* |Odstranit lokality appsetting *klíč* *název* <br/> Přidání webu appsetting *klíč*=*hodnotu* *název* |
| Seznam mobilních konfigurace *název* |seznam webů appsetting *název* |
| mobilní konfigurační získat *název* *klíč* |lokality zobrazit appsetting *klíč* *název* |
| mobilní konfigurační sady *název* *klíč* |Odstranit lokality appsetting *klíč* *název* <br/> Přidání webu appsetting *klíč*=*hodnotu* *název* |
| Seznam mobilních domén *název* |seznam domén lokality *název* |
| Přidat mobilní domény *název* *domény* |Přidání domény lokality *domény* *název* |
| Odstranění mobilních domény *název* |odstranění webu domény *domény* *název* |
| Zobrazit mobilních škálování *název* |Zobrazit lokality *název* |
| Změna mobilní škálování *název* |režim škálování webu *režimu* *název* <br /> lokality škálování instancí *instance* *název* |
| Seznam mobilních appsetting *název* |seznam webů appsetting *název* |
| Přidat mobilní appsetting *název* *klíč* *hodnota* |Přidání webu appsetting *klíč*=*hodnotu* *název* |
| Odstranit mobilní appsetting *název* *klíč* |Odstranit lokality appsetting *klíč* *název* |
| Zobrazit mobilních appsetting *název* *klíč* |Odstranit lokality appsetting *klíč* *název* |

Aktualizovat ověřování nebo nabízená oznámení nastavení aktualizace příslušné nastavení aplikace.
Úpravy souborů a publikování webu přes protokol ftp nebo git.

### <a name="diagnostics"></a>Protokolování a diagnostiky
Protokolování diagnostiky vypnutá normálně ve službě Azure App Service.  Chcete-li povolit protokolování diagnostiky:

1. Přihlaste se k portálu [portál Azure].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší migrované mobilní služby.
3. Otevře se okno nastavení ve výchozím nastavení.
4. Vyberte **diagnostické protokoly** v nabídce funkcí.
5. Klikněte na tlačítko **ON** pro tyto protokoly: **protokolování aplikace (systém souborů)**, **podrobné chybové zprávy**, a **trasování chybných požadavků**
6. Klikněte na tlačítko **systém souborů** pro protokolování webového serveru
7. Klikněte na **Uložit**.

K zobrazení protokolů:

1. Přihlaste se k portálu [portál Azure].
2. Vyberte **všechny prostředky** nebo **App Services** pak klikněte na název vaší migrované mobilní služby.
3. Klikněte **nástroje** tlačítko
4. Vyberte **datový proud protokolu** v nabídce dodržovat.

Protokoly se zobrazí v okně, jako jsou generovány.  Můžete také stáhnout protokoly pro pozdější analýzu pomocí svých přihlašovacích údajů nasazení. Další informace najdete v tématu [protokolování] dokumentaci.

## <a name="known-issues"></a>Známé problémy
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Odstraňování klon migrovat aplikace mobilních způsobí, že výpadek lokality
Pokud klonovat migrované mobilní službě pomocí prostředí Azure PowerShell a pak odstraňte klonu, odeberou se položky DNS pro vaši službu produkční.  Váš web je už nebude přístupný z Internetu.  

Řešení: Pokud chcete klonování váš web, to provést prostřednictvím portálu.

### <a name="changing-webconfig-does-not-work"></a>Změna souboru Web.config nefunguje
Pokud máte stránku ASP.NET, změny `Web.config` souboru získat nebyly použity.  Azure App Service vytvoří vhodný `Web.config` souboru během spouštění pro podporu runtime Mobile Services.  Určitá nastavení (například vlastní hlavičky) můžete přepsat pomocí transformace souboru XML.  Vytvořte soubor v názvem `applicationHost.xdt` – tento soubor musí skončit ve `D:\home\site` adresář služby Azure.  Nahrát `applicationHost.xdt` soubor pomocí vlastní nasazení skriptu nebo přímo pomocí modulu Kudu.  Na obrázku je dokument příklad:

```
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

Další informace najdete v tématu [XDT transformace ukázky] dokumentaci na Githubu.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Migrované Mobile Services nelze přidat do Traffic Manageru
Když vytvoříte profil Traffic Manageru, nemůžete vybrat, přímo migrované mobilní službě pro profil.  Použití "externí koncový bod."  Externí koncový bod lze přidat pouze pomocí prostředí PowerShell.  Další informace najdete v tématu [Traffic Manager kurzu](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Další kroky
Teď, když vaše aplikace je migrován do služby App Service, existují i další funkce, které můžete použít:

* Nasazení [přípravné sloty] povolit dvoufázové změny vašeho webu a provádět A / B testování.
* [WebJobs] zadejte náhradní server pro úlohy naplánované na vyžádání.
* Můžete [nepřetržitě nasazení] vaší lokality pomocí propojení na Githubu, sady TFS nebo Mercurial vaší lokality.
* Můžete použít [Application Insights] k monitorování vaší lokality.
* Zajišťují web a mobilní API z stejný kód.

### <a name="upgrading-your-site"></a>Upgrade webu služby Mobile Services pro Azure Mobile Apps SDK
* Pro projekty serveru na základě Node.js nové [Mobile Apps Node.js SDK] poskytuje několik nových funkcí. Například teď můžete provést místní vývoj a ladění, použít libovolná verze Node.js výše 0.10 a přizpůsobit pomocí veškerý middleware Express.js.
* Pro. Projekty serveru na základě NET, nové [balíčky NuGet sady SDK pro Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) mít větší flexibilitu v NuGet závislosti.  Tyto balíčky podporu ověřování nové služby App Service a vytvořit s žádným projektem technologie ASP.NET. Další informace o upgradu naleznete v tématu [upgradovat existující Mobile Service .NET do služby App Service](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[App Service – ceny]: https://azure.microsoft.com/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[Automatické škálování]: ../app-service/web-sites-scale.md
[Azure App Service]: ../app-service/app-service-web-overview.md
[portálu Azure Classic]: https://manage.windowsazure.com
[portál Azure]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/regions/
[Azure Scheduler plánuje]: ../scheduler/scheduler-plans-billing.md
[nepřetržitě nasazení]: ../app-service/app-service-continuous-deployment.md
[Převést smíšený obory]: https://azure.microsoft.com/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[vlastních názvů domén]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: http://www.telerik.com/fiddler
[obecné dostupnosti služby Azure App Service]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[protokolování]: ../app-service/web-sites-enable-diagnostic-log.md
[Mobile Apps Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[vs Mobile Services. Služby App Service]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notification Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[monitorování výkonu]: ../app-service/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[přípravné sloty]: ../app-service/web-sites-staged-publishing.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[XDT transformace ukázky]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[funkce]: ../azure-functions/functions-overview.md
