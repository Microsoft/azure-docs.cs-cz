---
title: Poznámky k verzi pro služby Azure BizTalk Services | Microsoft Docs
description: Jsou uvedené známé problémy pro služby Azure BizTalk Services
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: f4906fdc-4cd9-4a57-a007-a88c2e51a18f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 5c05c7c4c0f8f0b3619b36390c8a0224a03c900a
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063538"
---
# <a name="release-notes-for-azure-biztalk-services"></a>Poznámky k verzi pro služby Azure BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Poznámky k verzi pro služby Microsoft Azure BizTalk Services obsahují známé problémy v této verzi.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Co je nového v aktualizaci listopadu služby BizTalk Services
* Na portálu služby BizTalk lze povolit šifrování v klidovém stavu. V tématu [povolit šifrování v klidovém stavu portálu služby BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Aktualizace historie
### <a name="october-update"></a>Říjen aktualizace
* Účty organizace nejsou podporované:  
  * **Scénář**: jste zaregistrovali nasazení služby BizTalk pomocí účtu Microsoft (jako je user@live.com). V tomto scénáři můžete spravovat pouze uživatelé s Account Microsoft službu BizTalk pomocí portálu služby BizTalk Services. Nelze použít účet organizace.  
  * **Scénář**: jste zaregistrovali nasazení služby BizTalk pomocí organizačního účtu v Azure Active Directory (například user@fabrikam.com nebo user@contoso.com). V tomto scénáři můžete spravovat pouze uživatelé Azure Active Directory v rámci stejné organizace službu BizTalk pomocí portálu služby BizTalk Services. Nelze použít účet Microsoft.  
* Při vytváření služby BizTalk se automaticky registruje v portálu služby BizTalk.
  * **Scénář**: přihlásit k Azure, vytvoření služby BizTalk a potom vyberte **spravovat** velmi poprvé. Když se otevře na portálu služby BizTalk Services, služba BizTalk automaticky zaregistruje a je připravený pro vaše nasazení.  
    V tématu [registrace a aktualizace nasazení služby BizTalk v BizTalk Services portál](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Srpen 14 aktualizace
* Na portálu služby BizTalk teď odpojené smlouvy a most oddělení – obchodní partnery smlouvy a mostů. Teď vytvoříte smlouvy a mostů samostatně a v době běhu mostů odkazující na smlouvu na základě hodnot v EDI zprávy. V tématu [vytvoření smluv ve službě Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689908.aspx), [vytvořit mostu EDI pomocí portálu služby BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [vytvořit mostu AS2 pomocí portálu služby BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx)a [ Jak mostů vyřešit smlouvy za běhu?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* Možnost vytvoření šablony pro smlouvy je přerušeno.  
* Smlouvy na straně odesílání nyní můžete určit jiný oddělovač sady pro každé schéma. Tato konfigurace je zadaný v nastavení protokolu pro smlouvu straně odeslání. Další informace najdete v tématu [vytvořit na X12 smluv ve službě Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx) a [vytvořili smlouvu EDIFACT v Azure BizTalk Services](https://msdn.microsoft.com/library/azure/dn606267.aspx). Dva nové entity jsou také přidat do rozhraní API OM čip TPM k tomuto účelu. V tématu [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) a [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Standardní XSD konstrukce, včetně odvozené typy jsou nyní podporovány. V tématu [použijte standardní XSD vytvoří ve vašem mapy](https://msdn.microsoft.com/library/azure/dn793987.aspx) a [použití odvozené typy v mapování scénáře a příklady](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 podporuje nových algoritmů povinná kontrola úrovně Důvěryhodnosti pro podepisování zpráv a nový šifrovací algoritmus. V tématu [vytvořit ve službě Azure BizTalk Services smlouvu AS2](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
  ## <a name="know-issues"></a>Vědět problémy

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problémy s připojením k po aktualizaci portálu služby BizTalk Services
  Pokud máte na portálu služby BizTalk otevřete během upgradu služby BizTalk Services vrátit změny ve službě, může čelí problémy s připojením pomocí portálu služby BizTalk.  
  Jako řešení může znovu spustit prohlížeč, odstraňte mezipaměti prohlížeče nebo spuštění portálu v privátním režimu.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>Visual Studio IDE nelze najít artefakt, pokud kliknete na tlačítko chybě nebo upozornění v projektu služby BizTalk Services
Nainstalujte Visual Studio 2012 Update 3 RC 1 vyřešit problém.  

### <a name="custom-binding-project-reference"></a>Odkaz na projekt vlastní vazby
Vezměte v úvahu následující situace s projektem v řešení sady Visual Studio BizTalk Services:  

* Ve stejném řešení sady Visual Studio je projektu služby BizTalk Services a projekt vlastní vazby. Projektu služby BizTalk obsahuje odkaz na tento soubor projektu vlastní vazby.
* Projektu služby BizTalk obsahuje odkaz na vlastní vazby/chování knihovny DLL.

Sestavování, řešení v sadě Visual Studio úspěšně. Pak 'Znovu sestavte' nebo Vyčistit řešení. Poté když znovu sestavit nebo znovu vyčistit, dojde k následující chybě:  
  Nelze zkopírovat soubor <Path to DLL> na "bin\Debug\FileName.dll". Proces nemá přístup k souboru 'bin\Debug\FileName.dll', protože je stále používán jiným procesem.  

#### <a name="workaround"></a>Alternativní řešení
* Pokud [Visual Studio 2012 Update 3](https://docs.microsoft.com/en-us/visualstudio/releasenotes/vs2012-update3-vs) je nainstalovaná, máte následující dvě možnosti:
  
  * Restartujte Visual Studio, nebo
  * Restartujte řešení. Potom proveďte pouze sestavení v řešení.  
* Pokud [Visual Studio 2012 Update 3](https://docs.microsoft.com/en-us/visualstudio/releasenotes/vs2012-update3-vs) není nainstalovaná, otevřete Správce úloh, klikněte na tlačítko procesy, klikněte proces MSBuild.exe a potom klikněte na tlačítko Ukončit proces.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Směrování do koncových bodů BasicHttpRelay není podporován z mostů a portál služeb BizTalk povýšené netisknutelné znaky jako hlavičky protokolu HTTP
Pokud používáte netisknutelné znaky jako součást propagovaných vlastností pro zprávy, tyto zprávy nelze směrovat do předávání cílových míst, které používají BasicHttpRelay vazby. Navíc propagovaných vlastnosti které jsou k dispozici jako součást sledování jsou kódování URL pro objekty BLOB a zrušení kódovaného pro cílů.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN je odeslán asynchronně, i když není zaškrtnuta možnost MDN asynchronní odesílání
Vezměte v úvahu tento scénář – Pokud vyberete **odesílat asynchronní MDN** zaškrtávací políčko a zadejte adresu URL k odesílání asynchronní MDN na a potom zrušte zaškrtnutí políčka **odesílat asynchronní MDN** políčko znovu, MDN stále posílá Zadaná adresa URL, i když není vybrána možnost odesílat asynchronní MDNs.  
Jako alternativní řešení, musíte před zaškrtnutí políčka vymazat zadaná adresa URL **odesílat asynchronní MDN** zaškrtávací políčko a potom nasadíte smlouvy AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Prázdné znaky následující po platný výměnu způsobit prázdný zprávu k odeslání do koncového bodu pozastavit
Pokud jsou prázdné znaky nad rámec IEA segment, disassembler považován za konec aktuální výměnu a vypadá na další sadu prázdné znaky jako další zprávy. Vzhledem k tomu, že to není platný výměnu, můžete všimnout, že jednu úspěšné zprávu do cíle trasy jednu prázdnou zprávu je posílat a koncový bod pozastavit.  

### <a name="tracking-in-biztalk-services-portal"></a>Sledování na portálu služby BizTalk
Sledování události zaznamenání až zpracování EDI zprávy a všechny korelace. V případě selhání zprávu mimo fázi protokol sledování se zobrazí jako úspěšně dokončený. V této situaci, naleznete v části protokolu v části **podrobnosti** sloupec v **sledování** podrobnosti chyby.
X12 příjem a odesílání nastavení ([vytvořit na X12 smluv ve službě Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx)) poskytují informace o fázi protokolu.  

### <a name="update-agreement"></a>Smlouva pro aktualizaci
Na portálu služby BizTalk můžete upravit kvalifikátor Identity, pokud je nakonfigurován smlouvu. Výsledkem může být inconsistence vlastnosti. Například je smlouvu pomocí ZZ:1234567 a ZZ:7654321 kvalifikátor. V nastavení profilu portál služby BizTalk můžete změnit ZZ:1234567 být 01:ChangedValue. Otevřete smlouvu a zobrazí se místo ZZ:1234567 01:ChangedValue.
Chcete-li upravit kvalifikátor identity, smlouvu odstranit, aktualizovat **identity** v profilu partnera a pak znovu vytvořte smlouvy.  

> AZURE. UPOZORNĚNÍ toto chování ovlivňuje X12 a AS2.  
> 
> 

### <a name="as2-attachments"></a>AS2 příloh
Příloh pro AS2 zprávy nejsou podporovány v odeslat nebo přijmout. Konkrétně přílohy jsou bezobslužně ignorována a tělo zprávy zpracovávány jako regulární zprávu AS2.  

### <a name="resources-remembering-path"></a>Zdroje: Zapamatování cestu
Při přidávání **prostředky**, dialogového okna nemusí pamatovat cestu dříve použít k přidání prostředku. Pamatovat cestu dřív používal, zkuste přidat webové stránky portálu služeb BizTalk na **Důvěryhodné servery** v aplikaci Internet Explorer.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Pokud název entity mostu přejmenovat a zavřít projekt bez uložení změn, znovu otevřít entity výsledkem chyba
Představte si třeba situaci v následujícím pořadí:  

* Přidejte do projektu služby BizTalk mostu (například most XML One-Way)  
* Přejmenujte most a zadat hodnotu pro vlastnost název Entity. To přidružené .bridgeconfig soubor přejmenuje pomocí názvu, který jste zadali.  
* Zavřete soubor .bcs (ukončením kartě v sadě Visual Studio) bez uložení změn.  
* Znovu otevřete soubor .bcs v Průzkumníku řešení.  
  Si všimnete, že soubor přidružené .bridgeconfig se nový název, který jste zadali, název entity na návrhovou plochu, která je stále starý název. Pokud se pokusíte otevřít konfiguraci mostu poklepáním na komponentu most, zobrazí se následující chyba:  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist` Abyste se vyhnuli spuštěna v tomto scénáři, zkontrolujte, zda že po přejmenování entity v projektu služby BizTalk se uložit změny.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Sestavení projektu služby BizTalk se úspěšně, i když artefakt byl vyloučen z projektu sady Visual Studio
Představte si třeba situaci, kdy přidáte artefakt (například soubor XSD) do projektu služby BizTalk, zahrnují tento artefaktů v konfiguraci most? víc informací (například zadáním ji jako typ zprávy požadavku) a vyloučíte je z projektu sady Visual Studio. V takovém případě sestavení projektu poskytovat všechny chyby, dokud odstraněné artefaktů je k dispozici na disku na stejné umístění, ze kterých je zahrnutý v projektu sady Visual Studio.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>Projektu služby BizTalk nekontroluje dostupnost schématu při konfiguraci mostů
V projektu služby BizTalk Pokud schéma, které se přidá do projektu importuje jiné schéma projektu služby BizTalk nekontroluje, jestli importované schéma se přidá do projektu. Pokud se pokusíte sestavení tohoto projektu, se nezobrazí žádné chyby sestavení.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>Zpráva odpovědi pro požadavek-odpověď mostu XML je vždy charset UTF-8
U tohoto vydání je znaková sada zprávy s odpovědí z požadavku a odpovědi mostu XML vždycky nastavený na UTF-8.
  
### <a name="user-defined-datatypes"></a>Uživatelem definované datové typy
Sady BizTalk Adapter Pack adaptéry v rámci funkce služba BizTalk Adapter Service můžete využít uživatelem definované datové typy pro operace adaptéru.
Při použití uživatelem definované datové typy, zkopírujte soubory (.dll) na jednotku: \Program Files\Microsoft Service\BAServiceRuntime\bin\ adaptér BizTalk nebo k globální mezipaměti sestavení (GAC) na serveru, který hostuje službu služba BizTalk Adapter Service. K následující chybě může dojít v klientovi:  
```
<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
<faultcode>s:Client</faultcode>
<faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
<detail>
  <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
  </AFConnectRuntimeFault>
</detail>
</s:Fault>
```  
  
> [!IMPORTANT]
> Doporučujeme používat GACUtil.exe instalovat soubor do globální mezipaměti sestavení. GACUtil.exe dokumentů. použití tohoto nástroje a možnosti příkazového řádku Visual Studio.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Restartování webu BizTalk adaptér služby
Instalace **běh služby BizTalk adaptér*** vytvoří **služba BizTalk Adapter Service** web služby IIS, který obsahuje **BAService** aplikace. **BAService** aplikace interně používá předávací vazby k rozšiřují využitelnost místní koncový bod služby do cloudu. Jenom v případě, že spuštění místní služby, bude pro služby hostované na místních počítačích registrovaná odpovídající koncový bod předávání na Service Bus.  

Je-li zastavit a spustit aplikaci, není dodržení konfiguraci pro automatické spuštění aplikace. Takže když **BAService** je zastavena, vždy je nutné restartovat **služba BizTalk Adapter Service** místo webový server. Spuštění nebo zastavení **BAService** aplikace.

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Speciální znaky se nesmí používat pro adresu a entity názvy součástí LOB
Pro adresu a entity názvy součástí LOB byste neměli používat speciální znaky. Pokud tak učiníte, zobrazí se chyba při nasazení projektu služby BizTalk. Pro některé znaků jako "%", služba BizTalk Adapter Service Web může přejít do stavu ukončeno a budete muset spustit ručně.

### <a name="test-map-with-get-context-property"></a>Mapa test s vlastností kontextu Get
Pokud obsahuje transformace **získat vlastností kontextu** operace mapy, **testovací mapy** se nezdaří. Jako dočasné řešení, nahraďte **získat vlastností kontextu** mapy operaci s operací řetězení mapy řetězec obsahující fiktivní data. Tato akce naplnit je cílové schéma a povolit, že byste otestovat další funkce transformace.

### <a name="test-map-property-does-not-display"></a>Vlastnost mapy testovací nezobrazí.
**Testovací mapy** vlastnosti nezobrazovat v sadě Visual Studio. Tato situace může nastat, pokud **vlastnosti** okno a **Průzkumníku řešení** okno nejsou ukotveno současně. Chcete-li tento problém vyřešíte ukotvení **vlastnosti** a **Průzkumníku řešení** systému windows.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>Je zobrazeno šedě rozevíracího seznamu přeformátujte data a času
Když mapy operace přeformátujte DateTime je přidán do návrhové plochy a nakonfigurované, může šedě formátu rozevíracího seznamu. K tomu může dojít, pokud počítače zobrazení **Střední – 125 %** nebo **větší – 150 %**. Chcete-li vyřešit, nastavte zobrazení **menší – 100 % (výchozí)** pomocí následujících kroků:  

1. Otevřete **ovládací panely** a klikněte na tlačítko **vzhled a přizpůsobení**.
2. Klikněte na tlačítko **zobrazení**.
3. Klikněte na tlačítko **menší – 100 % (výchozí)** a klikněte na tlačítko **použít**.

**Formát** rozevíracího seznamu by měl nyní fungovat podle očekávání.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Duplicitní smluv na portálu služby BizTalk
Vezměte v úvahu následující scénář:

1. Vytvořte smlouvu pomocí Trading Partner Management OM API.
2. Otevřete smlouvu na portálu služby BizTalk ve dvou různých kartách.
3. Nasaďte smlouvy z obou karty.
4. V důsledku toho obě smlouvy nasadí výsledkem duplicitní položky na portálu služby BizTalk

**Alternativní řešení**. Otevřete některého duplicitní smlouvy na portálu služby BizTalk a zrušit nasazení.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Mosty nepoužívejte aktualizovaný certifikát i po aktualizaci se certifikát v úložišti artefaktů
Zvažte následující scénáře:  

**Scénář 1: Použití certifikátů na základě kryptografického otisku pro přenos zpráv z mostu pro koncový bod služby zabezpečení**  
Vezměte v úvahu scénář, kde používat certifikáty založené na kryptografický otisk ve vašem projektu služby BizTalk. Aktualizovat certifikát na portálu služby BizTalk se stejným názvem, ale jiný kryptografický otisk, ale neaktualizují projektu služby BizTalk odpovídajícím způsobem. V takové situaci most může pokračovat ve zpracování zpráv, protože starší data certifikátu může být v mezipaměti kanálu. Potom se nezdaří zpracování zprávy.  

**Alternativní řešení**: aktualizovat certifikát v projektu služby BizTalk a znovu nasaďte projekt.  

**Scénář 2: Pomocí chování na základě názvu k identifikaci certifikáty pro přenos zpráv z mostu pro koncový bod služby zabezpečení**

Představte si třeba situaci, kdy používáte chování na základě názvu k identifikaci certifikáty ve vašem projektu služby BizTalk. Aktualizovat certifikát na portálu služby BizTalk však neaktualizují projektu služby BizTalk odpovídajícím způsobem. V takové situaci most může pokračovat ve zpracování zpráv, protože starší data certifikátu může být v mezipaměti kanálu. Potom se nezdaří zpracování zprávy.  

**Alternativní řešení**: aktualizovat certifikát v projektu služby BizTalk a znovu nasaďte projekt.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Mosty pokračovat ve zpracování zprávy i v případě, že databáze SQL je offline
BizTalk Services mostů pokračovat ve zpracování zprávy nějakou dobu, i v případě, že službě Microsoft Azure SQL Database (které se ukládají informace o spuštěných jako nasazené artefaktů a kanály), je offline. Je to proto, že služba BizTalk Services používá v mezipaměti artefakty a most konfigurace.
Pokud nechcete, aby mostů zpracovat všechny zprávy, když databáze SQL je offline, můžete použít rutiny prostředí PowerShell služby BizTalk pro zastavení nebo pozastavení služby BizTalk. V tématu [Ukázka správy služby Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329019) pro rutiny prostředí Windows PowerShell ke správě operací.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Čtení zprávy XML v rámci komponenty vlastního kódu most obsahuje znak navíc BOM
Vezměte v úvahu scénář, kde chcete číst zprávu XML v rámci vlastního kódu most. Pokud používáte System.Text.Encoding.UTF8.GetString(bytes) rozhraní API .NET je jeden znak navíc BOM součástí výstup na začátku zprávy. Ano, pokud nechcete, aby výstup zahrnout další znak BOM, musíte použít ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Odesílání zpráv do mostu pomocí WCF se nedá použít.
Zprávy odeslané do mostu pomocí WCF se nedá použít. Místo toho byste měli používat HttpWebRequest – Pokud chcete, aby škálovatelné klienta.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>UPGRADE: Informace o chybě zprostředkovatele tokenu po provedení upgradu z verze Preview služby BizTalk k obecné dostupnosti (GA)
S active dávky je EDI nebo smlouvy AS2. Když služba BizTalk je upgrade z verze Preview na GA, může dojít následující:

* Chyba: Poskytovatel tokenu se nepodařilo poskytnout token zabezpečení. Zprostředkovatel tokenu vrátil zprávu: vzdálený název nelze rozpoznat.
* Úkoly služby batch, se zruší.

**Alternativní řešení**: po služba BizTalk Service se aktualizuje na obecné dostupnosti (GA), znovu nasaďte smlouvu.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>UPGRADE: Sada nástrojů zobrazí původní ikony most po upgradu sady SDK služby BizTalk
Po upgradu starší verzi sady SDK služby BizTalk měl původní ikony představující mostů, i nadále zobrazit staré ikony mostů sady nástrojů. Ale pokud přidáte mostu na plochu návrháře projektu služby BizTalk, povrchu zobrazuje na ikonu nový.  

**Alternativní řešení**. Tento problém můžete vyřešit odstraněním .tbd souborů v části <system drive>: \Users\<uživatele > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>UPGRADE: Aktualizace BizTalk portálu z verze Preview GA může zobrazit chyba oznamující, že není k dispozici možnost EDI
Pokud jste přihlášení k portálu služby BizTalk BizTalk Services je upgradu z verze Preview do GA, může se na portálu zobrazí následující chyba:  

Tato funkce není k dispozici jako součást této edici služby Microsoft Azure BizTalk Services. Chcete-li použít tyto možnosti přepněte na příslušnou verzi.  

**Řešení**: při odhlášení z na portálu, zavřete a otevřete prohlížeč a přihlaste se k portálu.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>UPGRADE: Nová data sledování nezobrazuje po upgradu služby BizTalk Services na GA
Předpokládejme situaci, kdy máte XML most nasazené v předplatném Preview služby BizTalk. Odeslání zprávy do mostu a odpovídající sledování dat je k dispozici na portálu služby BizTalk. Nyní pokud modul runtime bits portál služby BizTalk a BizTalk Services upgradují na GA a odeslat zprávu na stejný koncový bod most předtím, data sledování nezobrazuje pro zprávy odeslané po upgradu.  

### <a name="pipelines-versus-bridges"></a>Kanály versus mostů
V tomto dokumentu se termín 'kanálů' a 'mostů' používají zcela zaměnitelným významem. I v podstatě mají stejný význam, který je nasazen na služby BizTalk Services jednotku zpracování zprávy.  

### <a name="concepts"></a>Koncepty
[BizTalk Services](https://msdn.microsoft.com/library/azure/hh689864.aspx)   

