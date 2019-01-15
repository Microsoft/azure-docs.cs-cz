---
title: Poznámky k verzi pro služby Azure BizTalk Services | Dokumentace Microsoftu
description: Jsou uvedené známé problémy pro Azure BizTalk Services
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
ms.openlocfilehash: 10e790c2edb22b3c7926216535d76c50261589f3
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260315"
---
# <a name="release-notes-for-azure-biztalk-services"></a>Poznámky k verzi pro služby Azure BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Poznámky k verzi pro služby Microsoft Azure BizTalk Services obsahují známé problémy v této verzi.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Co je nového v aktualizaci z listopadu BizTalk Services
* Šifrování v klidovém stavu lze povolit v portálu BizTalk Services. Zobrazit [povolit šifrování v klidovém stavu portálu BizTalk Services](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Historie aktualizace
### <a name="october-update"></a>Říjen aktualizace
* Jsou podporovány účty organizace:  
  * **Scénář**: Jste zaregistrovali nasazení služby BizTalk pomocí účtu Microsoft (třeba user@live.com). V tomto scénáři můžete spravovat pouze uživatelé s Account Microsoft službu BizTalk pomocí portálu BizTalk Services. Nelze použít účet organizace.  
  * **Scénář**: Jste zaregistrovali nasazení služby BizTalk pomocí účtu organizace v Azure Active Directory (například user@fabrikam.com nebo user@contoso.com). V tomto scénáři můžete spravovat pouze uživatelé Azure Active Directory v rámci stejné organizace služby BizTalk pomocí portálu BizTalk Services. Nelze použít účet Microsoft.  
* Při vytváření služby BizTalk, budete automaticky zaregistrováni v portálu BizTalk Services.
  * **Scénář**: Přihlášení k Azure, vytvořte službu BizTalk a pak vyberte **spravovat** velmi poprvé. Při otevření portálu BizTalk Services, služba BizTalk automaticky zaregistruje a je připravená pro vaše nasazení.  
    Zobrazit [registrace a aktualizace nasazení služby BizTalk na BizTalk Services – portál](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>14. srpna aktualizace
* V portálu BizTalk Services jsou teď oddělení smlouvy a most oddělení – obchodní partner smluv a přemostění. Teď vytvoříte smluv a přemostění samostatně a za běhu mosty řešení smlouvy na základě hodnot v zpráv EDI. Naleznete v tématu [vytvoření smluv ve službě Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689908.aspx), [vytvořit most EDI pomocí portálu BizTalk Services](https://msdn.microsoft.com/library/azure/dn793986.aspx), [vytvořit most AS2 pomocí portálu BizTalk Services](https://msdn.microsoft.com/library/azure/dn793993.aspx)a [ Jak vyřešit mosty smlouvy za běhu?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* Možnost vytvoření šablony pro smlouvy je přerušeno.  
* Straně odesílání smlouvy teď můžete zadat jiné oddělovače skupin pro každé schéma. Tato konfigurace je zadaný v nastavení protokolu pro dohody straně odeslání. Další informace najdete v tématu [vytvořit příslušný X12 smlouvy o utajení, služby Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx) a [vytvořili ve službě Azure BizTalk Services smlouvu EDIFACT](https://msdn.microsoft.com/library/azure/dn606267.aspx). Dvě nové entity, které jsou také přidány do rozhraní API TPM OM ke stejnému účelu. Zobrazit [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) a [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Standardní XSD konstrukcím, včetně odvozené typy jsou nyní podporovány. Zobrazit [použijte standardní XSD vytvoří v mapách](https://msdn.microsoft.com/library/azure/dn793987.aspx) a [použijte odvozené typy v mapování scénářů a příklady](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 podporuje nový algoritmus MIC pro podepisování zpráv a nových šifrovacích algoritmů. Zobrazit [vytvořili ve službě Azure BizTalk Services smlouvu AS2](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
  ## <a name="know-issues"></a>Zjistit problémy

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problémy s připojením po aktualizaci portálu služby BizTalk Services
  Pokud máte portálu BizTalk Services, otevřít, zatímco služby BizTalk Services se upgraduje na vrácení se změnami do služby, může setkat problémy s připojením pomocí portálu BizTalk Services.  
  Jako alternativní řešení může restartujte prohlížeč, odstraníte mezipaměť prohlížeče nebo spustit na portálu v privátním režimu.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>IDE sady Visual Studio nemůže najít artefakt, vyberete-li chybu nebo upozornění v projektu služby BizTalk Services
Nainstalujte Visual Studio 2012 Update 3 RC 1 k vyřešení problému.  

### <a name="custom-binding-project-reference"></a>Odkaz na projekt vlastní vazby
Vezměte v úvahu s BizTalk Services projektu v řešení sady Visual Studio v následujících případech:  

* Ve stejném řešení sady Visual Studio je projekt služby BizTalk Services a vlastních vazeb projektu. Služba BizTalk projekt obsahuje odkaz na tento soubor projektu vlastní vazby.
* Služba BizTalk projekt obsahuje odkaz na vlastní vazby a chování knihovny DLL.

"Sestavování" řešení v sadě Visual Studio úspěšně. Potom "Sestavení" nebo Vyčistit řešení. Potom když znovu sestavit nebo znovu, vyčistit došlo k následující chybě:  
  Nelze zkopírovat soubor <Path to DLL> na "bin\Debug\FileName.dll". Proces nemá přístup k souboru 'bin\Debug\FileName.dll', protože je používán jiným procesem.  

#### <a name="workaround"></a>Alternativní řešení
* Pokud [Visual Studio 2012 Update 3](https://docs.microsoft.com/visualstudio/releasenotes/vs2012-update3-vs) je nainstalovaný, máte následující dvě možnosti:
  
  * Restartujte Visual Studio, nebo
  * Znovu spusťte řešení. Potom proveďte pouze sestavení řešení.  
* Pokud [Visual Studio 2012 Update 3](https://docs.microsoft.com/visualstudio/releasenotes/vs2012-update3-vs) není nainstalovaná, otevřete Správce úloh, procesy, které kartu, a klepněte na procesů MSBuild.exe, klikněte na tlačítko Ukončit proces.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Směrování do koncových bodů BasicHttpRelay není podporován z přemostění a portálu BizTalk Services netisknutelné znaky jsou povýšeny jako hlavičky protokolu HTTP
Pokud používáte netisknutelné znaky jako součást propagované vlastnosti zprávy, tyto zprávy nejde směrovat do cíle relay, používající BasicHttpRelay vazby. Navíc propagované vlastnosti, které jsou k dispozici jako součást sledování se zakódovanými do adresy URL pro objekty BLOB a zrušení kódovaného pro cíle.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>Zprávy MDN je odeslán asynchronně, i v případě, že není zaškrtnuta možnost odeslat asynchronní zprávu MDN
Vezměte v úvahu tento scénář – Pokud vyberete **odesílat asynchronní zprávy MDN** zaškrtávací políčko a zadejte adresu URL odeslat asynchronní zprávu MDN a potom zrušte zaškrtnutí políčka **odesílat asynchronní zprávy MDN** zaškrtávací políčko znovu MDN stále posílá Zadaná adresa URL i v případě, že není vybraná možnost odesílat asynchronní něho.  
Jako alternativní řešení, musí před zrušením zaškrtnutí zrušte zadanou adresu URL **odesílat asynchronní zprávy MDN** zaškrtávací políčko a potom nasaďte smlouvy AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Prázdné znaky nad rámec platný výměny způsobit prázdný zprávy k odeslání do koncového bodu pozastavení
Pokud jsou prázdné znaky nad rámec segmentu IEA, disassembler považován za konec aktuální výměny a zobrazuje další sadu prázdnými znaky jako další zpráva. Protože toto není platný výměny, všimnout, že jeden úspěšné zpráva se odešle do cíle trasy a jedna prázdná zpráva přijde koncového bodu pozastavení.  

### <a name="tracking-in-biztalk-services-portal"></a>Sledování portálu BizTalk Services
Sledování události mají zachytávat až po zpracování zprávy EDI a jakákoli korelace. Pokud zpráva neprojde mimo fázi protokolu, se zobrazí jako úspěšně dokončený sledování. V této situaci, přečtěte si část protokolu v části **podrobnosti** sloupec v **sledování** pro podrobnosti o chybě.
X12 příjem a odesílání nastavení ([vytvořit příslušný X12 smlouvy o utajení, služby Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689847.aspx)) poskytují informace o fázi protokolu.  

### <a name="update-agreement"></a>Aktualizace smlouvy
Portálu BizTalk Services umožňuje změnit kvalifikátor identitu, pokud je nakonfigurovaná smlouvu. To může způsobit nekonzistentní vlastnosti. Například je pomocí ZZ:1234567 a ZZ:7654321 kvalifikátor smlouvu. V nastavení profilu portálu BizTalk Services můžete změnit ZZ:1234567 bude 01:ChangedValue. Otevřete smlouvu a zobrazí se místo ZZ:1234567 01:ChangedValue.
Upravit kvalifikátor identitu, odstranit smlouvu, aktualizujte **identit** profil partnera a pak znovu vytvořte smlouvy.  

> AZURE. Upozornění: Toto chování má vliv na X12 a AS2.  
> 
> 

### <a name="as2-attachments"></a>Přílohy AS2
Přílohy pro AS2 zpráv nejsou podporovány v odeslání nebo přijetí. Konkrétně jsou přílohy tiše ignorováno a text zprávy je zpracovávat jako regulární zpráva AS2.  

### <a name="resources-remembering-path"></a>Prostředky: Příslušná cesta
Při přidávání **prostředky**, dialogové okno nemusí si vzpomenout na cestu dříve použít k přidání prostředku. Pamatovat cestu dříve používali, zkuste přidat k webu portálu BizTalk Services **Důvěryhodné servery** v aplikaci Internet Explorer.  

### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Je-li přejmenovat název entity most a zavřít projekt bez uložení změn, znovu otevřete entitu způsobí chybu
Představte si třeba situaci v následujícím pořadí:  

* Most (například most XML One-Way) přidejte do projektu služby BizTalk  
* Most přejmenujte a zadat hodnotu pro vlastnost název Entity. To přejmenuje přidružené .bridgeconfig soubor s názvem, který jste zadali.  
* Zavřete soubor .bcs (když zavřete kartu v sadě Visual Studio) bez uložení změn.  
* Z Průzkumníku řešení otevřete soubor .bcs znovu.  
  Můžete si všimnout, že zatímco přidružené .bridgeconfig soubor má název, který jste zadali, názvu entity na návrhové ploše je stále starý název. Pokud se pokusíte spustit nástroj konfigurace mostu dvojitým kliknutím na součást most, zobrazí se následující chyba:  
  `‘<old name>’ Entity’s associated file ‘<old name>.bridgeconfig’ does not exist` Aby se zabránilo spouštění v tomto scénáři, zkontrolujte, zda že po přejmenování entity v projektu služby BizTalk se uložit změny.  
  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Služba BizTalk projekt vytvoří úspěšně i v případě, že artefakt byl vyloučen z projektu sady Visual Studio
Představte si třeba situaci, kde můžete přidávat artefakt (například soubor XSD) do projektu služby BizTalk, do konfigurace mostu zahrnout tento artefakt (například tak, že zadáte jeho jako typu požadavku zprávy) a vyloučit ho z projektu sady Visual Studio. V takovém případě se sestavení projektu nebude dát všechny chyby jako odstraněné artefakt je k dispozici na disku na stejné umístění, ve kterém je zahrnutý v projektu sady Visual Studio.
  
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>Projekt služby BizTalk nekontroluje dostupnost schématu při konfiguraci přemostění
V projektu služby BizTalk Pokud schéma, které se přidá do projektu importuje jiného schématu projektu služby BizTalk nekontroluje, jestli je importovaného schématu přidat do projektu. Pokud se pokusíte k vytvoření tohoto projektu, se nezobrazí žádné chyby buildu.
  
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>Zpráva odpovědi pro požadavek-odpověď most XML je vždy znaková sada UTF-8
U tohoto vydání je znaková sada zprávy s odpovědí z most požadavku a odpovědi XML vždycky nastavený na UTF-8.
  
### <a name="user-defined-datatypes"></a>Uživatelem definované datové typy
Sady BizTalk Adapter Pack adaptéry v rámci funkci služby BizTalk Adapter Service se můžou využívat uživatelem definované datové typy pro operace adaptéru.
Při použití uživatelem definované datové typy, zkopírujte soubory (.dll) na disku: \Program Files\Microsoft Service\BAServiceRuntime\bin\ adaptér BizTalk nebo chcete globální mezipaměti sestavení (GAC) na serveru, který hostuje službu BizTalk Adapter service. V opačném případě může dojít k následující chybě na straně klienta:  
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
> Je doporučeno použití GACUtil.exe k instalaci souboru do globální mezipaměti sestavení. GACUtil.exe dokumenty o použití tohoto nástroje a možnosti příkazového řádku sady Visual Studio.  
> 
> 

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Restartování webu služby adaptér BizTalk
Instalace **běhu služby adaptér BizTalk*** vytvoří **služby BizTalk Adapter Service** webovou stránku služby IIS, který obsahuje **BAService** aplikace. **BAService** vazby přenosu aplikace interně používá k rozšíření dosahu místních koncového bodu služby do cloudu. Služby hostované v místním odpovídající koncový bod relay zaregistruje ve službě Service Bus pouze při spuštění místní služby.  

Je-li zastavit a spustit aplikaci, konfiguraci automatického spuštění aplikace není podporována. Takže když **BAService** je zastaven, vždy je nutné restartovat **služby BizTalk Adapter Service** webu místo. Spuštění nebo zastavení **BAService** aplikace.

### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>Speciální znaky by neměla používat pro názvy adresu a entit LOB komponent
Pro názvy adresu a entit LOB součásti byste neměli používat speciální znaky. Pokud tak učiníte, zobrazí se chyba při nasazování projektu služby BizTalk. Pro některé znaky, jako je '%', na webu služby BizTalk Adapter Service je možné dát do stavu Zastaveno a budete muset spustit ručně.

### <a name="test-map-with-get-context-property"></a>Test mapy se vlastnost kontextu Get
Pokud obsahuje transformace **získat kontextové vlastnosti** mapy operace **Test mapování** se nezdaří. Dočasným řešením nahradit **získat vlastnost Context** mapy operace s operací zřetězit mapy řetězec obsahující fiktivní data. Tím naplnit na cílové schéma a umožní že otestovat další funkce transformace.

### <a name="test-map-property-does-not-display"></a>Vlastnost testu mapy nezobrazí.
**Test mapování** vlastnosti se nezobrazí v sadě Visual Studio. Tato situace může nastat, pokud **vlastnosti** okno a **Průzkumníka řešení** nejsou okno ukotveno současně. Chcete-li tento problém vyřešit, ukotvit **vlastnosti** a **Průzkumníka řešení** systému windows.  

### <a name="datetime-reformat-drop-down-is-grayed-out"></a>Datum a čas přeformátovat rozevíracího seznamu nejde aktivovat.
Při operaci mapy opakovaně formátovat datum a čas je přidán na návrhovou plochu a nakonfigurované, může šedě formátu rozevíracího seznamu. K tomu může dojít, pokud počítač zobrazení nastavený **Střední – 125 %** nebo **větší – 150 %**. Pokud chcete vyřešit, nastavte zobrazení na **menší – 100 % (výchozí)** pomocí následujících kroků:  

1. Otevřít **ovládací panely** a klikněte na tlačítko **vzhled a přizpůsobení**.
2. Klikněte na tlačítko **zobrazení**.
3. Klikněte na tlačítko **menší – 100 % (výchozí)** a klikněte na tlačítko **použít**.

**Formátu** rozevírací seznam by měl nyní fungovat podle očekávání.

### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Duplicitní smluv v portálu BizTalk Services
Vezměte v úvahu následující scénář:

1. Vytvořili smlouvu pomocí obchodní Partner objektový model rozhraní API pro správu.
2. Otevřete smlouvu v portálu BizTalk Services na dvou různých kartách.
3. Nasazení smlouvy z obou kartách.
4. V důsledku toho obou smlouvy nasadí, což vede k duplicitní položky v portálu BizTalk Services

**Alternativní řešení**. Otevřete kterékoli z duplicitních smluv v portálu BizTalk Services a zrušení.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Mosty nepoužívejte aktualizovaný certifikát, i když byl aktualizován certifikát v úložišti artefaktů
Zvažte následující scénáře:  

**Scénář 1: Použití certifikátů na základě kryptografického otisku pro přenos zpráv z most do koncového bodu služby zabezpečení**  
Představte si třeba situaci, kdy používat certifikáty založené na kryptografický otisk v projektu služby BizTalk. Aktualizace certifikátu v portálu BizTalk Services se stejným názvem, ale jiný kryptografický otisk, ale neaktualizují projektu služby BizTalk odpovídajícím způsobem. V takovém scénáři může nadále most zpracování zpráv, protože starší data certifikátu může být stále v mezipaměti kanálu. Potom se nezdaří zpracování zpráv.  

**Alternativní řešení**: Aktualizace certifikátu v projektu služby BizTalk a projekt znovu nasadit.  

**Scénář 2: Pomocí chování na základě název k identifikaci certifikáty pro zabezpečení přenosu zpráv z most do koncového bodu služby**

Představte si třeba situaci, kde používáte chování na základě název pro identifikaci certifikáty v projektu služby BizTalk. Aktualizace certifikátu v portálu BizTalk Services, ale neaktualizují projektu služby BizTalk odpovídajícím způsobem. V takovém scénáři může nadále most zpracování zpráv, protože starší data certifikátu může být stále v mezipaměti kanálu. Potom se nezdaří zpracování zpráv.  

**Alternativní řešení**: Aktualizace certifikátu v projektu služby BizTalk a projekt znovu nasadit.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Mosty pokračovat ve zpracování zprávy i v případě, že databáze SQL je offline
Mosty BizTalk Services i nadále zpracovávat zprávy na dobu, i v případě, že Microsoft Azure SQL Database (která obsahuje provozní informace, například nasazené artefakty a kanály), je offline. Toto je vzhledem k tomu BizTalk Services se používá v mezipaměti artefakty a konfigurace mostu.
Pokud nechcete, aby mosty zpracovávat všechny zprávy, když databáze SQL je offline, můžete použít rutiny prostředí PowerShell BizTalk Services pro zastavení nebo pozastavení služby BizTalk. Zobrazit [Ukázka správy služby Azure BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=329019) pro rutiny Windows Powershellu ke správě operací.  

### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Čtení zprávy XML v rámci komponenty mostu vlastní kód obsahuje znak navíc BOM
Představte si třeba situaci, kdy si chcete přečíst zprávu XML v rámci mostu vlastní kód. Pokud používáte System.Text.Encoding.UTF8.GetString(bytes) rozhraní API .NET je znak navíc BOM součástí výstupu na začátek zprávy. Ano, pokud nechcete, aby výstup do další znak BOM, je nutné použít ```System.IO.StreamReader().ReadToEnd()```.

### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Odesílání zprávy na most pomocí technologie WCF škálování
Zprávy odeslané do most pomocí technologie WCF škálování. Pokud chcete škálovatelné klient je vhodné použít HttpWebRequest.

### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>UPGRADE: Chyba zprostředkovatele tokenu po upgradu portálu BizTalk Services ve verzi Preview do obecné dostupnosti (GA)
Existuje smlouvy AS2 nebo EDI s aktivní dávky. Když službu BizTalk se upgraduje z verze Preview verzi GA, může dojít k následující:

* Chyba: Poskytovatel tokenu nemohl poskytnout token zabezpečení. Poskytovatel tokenů vrátil zprávu: Vzdálený název se nedá rozpoznat.
* Úkoly služby batch se zruší.

**Alternativní řešení**: Po aktualizaci na obecné dostupnosti (GA) službě BizTalk znovu nasaďte smlouvy.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>UPGRADE: Panel nástrojů zobrazí staré ikony most po upgradu sadu SDK pro BizTalk Services
Poté, co upgradujete dřívější verzi sady SDK služby BizTalk měli staré ikony reprezentující překročení mostu, panelu pořád zobrazovat staré ikony přemostění. Ale pokud přidáte mostu na plochu návrháře projektu služby BizTalk, na plochu ukazuje na ikonu nový.  

**Alternativní řešení**. Tento problém můžete obejít tak, že odstraníte soubory do .tbd <system drive>: \Users\<uživatele > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>UPGRADE: Aktualizace portálu BizTalk z verze Preview verzi GA, může zobrazit chyba oznamující, že není k dispozici možnost EDI
Pokud jste přihlášení do portálu BizTalk Services a BizTalk Services se upgraduje z verze Preview verzi GA, může na portálu zobrazí následující chyba:  

Tato možnost není k dispozici jako součást této edici systému Microsoft Azure BizTalk Services. Chcete-li použít tyto možnosti přepnout na příslušnou verzi.  

**Řešení:** Odhlaste se z portálu, zavřete a otevřete prohlížeč a přihlaste se do portálu.  

### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>UPGRADE: Nová data sledování nezobrazuje po BizTalk Services se upgraduje na verzi GA
Účely Předpokládejme scénář, kde se nachází XML most nasazené v předplatném BizTalk Services ve verzi Preview. Odesílat zprávy na most a odpovídající sledování dat je k dispozici na portálu BizTalk Services. Nyní pokud modul runtime bits portálu BizTalk Services a BizTalk Services jsou upgradovány na verzi GA a odeslat zprávu o stejný koncový bod most předtím nasadili, data sledování není uveden pro zprávy odeslané po upgradu.  

### <a name="pipelines-versus-bridges"></a>Kanály a přemostění
V tomto dokumentu termín "kanály" a "edice" zaměňují. I v podstatě stejný význam, který je nasazený na BizTalk Services jednotka zpracování zprávy.  

### <a name="concepts"></a>Koncepty
[BizTalk Services](https://msdn.microsoft.com/library/azure/hh689864.aspx)   

