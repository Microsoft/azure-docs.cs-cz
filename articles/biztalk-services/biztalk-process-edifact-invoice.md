---
title: 'Kurz: Zpracování faktur EDIFACT pomocí služby Azure BizTalk Services | Dokumentace Microsoftu'
description: Jak vytvořit a nakonfigurovat konektor pole nebo rozhraní API app a použít jej v aplikaci logiky ve službě Azure App Service
services: biztalk-services
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: biztalk-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
ms.openlocfilehash: bb07e3ab8043aab24d6d8c3e3db3f3674b28c6f3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244487"
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Kurz: Zasílání faktur procesu EDIFACT pomocí Azure BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Portálu BizTalk Services můžete použít ke konfiguraci a nasazení X12 a smlouvy EDIFACT. V tomto kurzu se podíváme na tom, jak vytvořit smlouvu EDIFACT pro výměnu faktury mezi obchodními partnery. Tento kurz je napsán kolem začátku do konce obchodní řešení zahrnující dvě obchodní partneři, Northwind a Contoso, které výměna zpráv EDIFACT.  

## <a name="sample-based-on-this-tutorial"></a>Ukázka podle tohoto kurzu
Tento kurz je napsán kolem ukázku, **odesílání EDIFACT faktury pomocí BizTalk Services**, která je k dispozici ke stažení z [galerie kódů MSDN](https://go.microsoft.com/fwlink/?LinkId=401005). Můžete použitím této ukázky a projít tento kurz a pochopit, jak byla sestavena vzorku. Nebo, v tomto kurzu můžete použít k vytvoření vlastních řešení základů. V tomto kurzu je zaměřena na druhý přístup tak, že chápete, jak toto řešení byla vytvořena. Také co je to možné, tento kurz je konzistentní s ukázkou a používá stejné názvy pro artefakty (například schémata, transformací) jako v ukázce.  

> [!NOTE]
> Protože toto řešení zahrnuje odesílá zprávu do mostu EDI z přemostění EAI, znovu použije [BizTalk Services most řetězení ukázka](https://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) vzorku.  
> 
> 

## <a name="what-does-the-solution-do"></a>Co dělá řešení?
V tomto řešení obdrží Northwind faktur EDIFACT ze společnosti Contoso. Tyto faktury nejsou ve standardním formátu EDI. Ano před odesláním faktury Northwind, ho musí transformuje na dokument EDIFACT faktury (také nazývané INVOIC). Po obdržení musí Northwind zpracování faktur EDIFACT a vracet ovládacího prvku zprávy (také nazývané CONTRL) na Contoso.

![][1]  

Společnost Contoso využívá k dosažení této podnikový scénář, funkce poskytované službou Microsoft Azure BizTalk Services.

* Společnost Contoso využívá přemostění EAI k transformaci původní fakturu EDIFACT INVOIC.
* Přemostění EAI pak odešle zprávu do mostu odeslat EDI nasazen jako součást smlouvy nakonfigurované v portálu BizTalk Services.
* Odeslat most EDI zpracovává EDIFACT INVOIC a směruje do Northwind.
* Po přijetí faktury, Northwind vrátí zprávu CONTRL EDI přijímat most nasazen jako součást smlouvy.  

> [!NOTE]
> Volitelně můžete toto řešení ukazuje také použití dávkování odesílat faktury v dávkách, místo abyste odesílali každé faktuře samostatně.  
> 
> 

K dokončení tohoto scénáře, můžeme používat fronty Service Bus fakturu odešlete ze společnosti Contoso Northwind nebo přijetí potvrzení z databáze Northwind. Tyto fronty je možné vytvářet pomocí klientské aplikace, která je k dispozici ke stažení a je součástí ukázkového balíčku, který je k dispozici v rámci tohoto kurzu.  

## <a name="prerequisites"></a>Požadavky
* Musíte mít obor názvů služby Service Bus. Pokyny k vytvoření oboru názvů, naleznete v tématu [postupy: vytvoření nebo úpravě Namespace služby Service Bus služby](https://msdn.microsoft.com/library/azure/hh674478.aspx). Předpokládejme, že už máte obor názvů Service Bus, které jsou zřízené, volá **edifactbts**.
* Musíte mít předplatné služby BizTalk Services. Pro účely tohoto kurzu Předpokládejme, máte předplatné služby BizTalk Services, volá **contosowabs**.
* Zaregistrujte své předplatné služby BizTalk Services na portálu BizTalk Services. Pokyny najdete v tématu [registrace nasazení služby BizTalk na portálu BizTalk Services](https://msdn.microsoft.com/library/hh689837.aspx)
* Musíte mít nainstalovanou sadu Visual Studio.
* Je nutné nainstalovat sadu SDK pro BizTalk Services. Můžete stáhnout sady SDK z [http://go.microsoft.com/fwlink/?LinkId=235057](https://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Krok 1: Vytvoření fronty služby Service Bus
Toto řešení používá fronty Service Bus umožňuje výměnu zpráv mezi obchodními partnery. Contoso a Northwind odesílání zpráv do front z kde je využívat mosty EAI a EDI. Pro tohle řešení musíte tři fronty služby Service Bus:

* **northwindreceive** – Northwind obdrží fakturu ze společnosti Contoso prostřednictvím této fronty.
* **contosoreceive** – Contoso přijme potvrzení z databáze Northwind přes tuto frontu.
* **Pozastavit** – všechny pozastavené zprávy jsou směrovány do této fronty. Zprávy jsou pozastavené, pokud selžou během zpracování.

Tyto fronty služby Service Bus můžete vytvořit pomocí klientské aplikace součástí ukázkového balíčku.  

1. Z umístění, kam jste stáhli ukázku, otevřete **kurzu zasílání faktur pomocí BizTalk Services EDI Bridges.sln**.
2. Stisknutím klávesy **F5** sestavit a spustit **kurzu klienta** aplikace.
3. V dialogovém okně zadejte obor názvů služby Service Bus ACS, název vystavitele a klíč vystavitele.
   
   ![][2]  
4. Okno se zprávou zobrazí výzvu, tři fronty budou vytvořeny v oboru názvů služby Service Bus. Klikněte na **OK**.
5. Ponechte kurzu klienty v provozu. Otevřít, klikněte na tlačítko **služby Service Bus** > ***oboru názvů služby Service Bus*** > **fronty**a ověřte, že se vytvoří tři fronty.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Krok 2: Vytvoření a nasazení smlouvy s obchodním partnerem
Vytvoření smlouvy s obchodním partnerem mezi Contoso a Northwind. Smlouvy s obchodním partnerem definuje kontrakt kompromisy mezi dvěma obchodními partnery, jako je například které zprávy schématu se má použít, který protokol zasílání zpráv chcete použít, atd. Smlouvy s obchodním partnerem obsahuje dva mosty EDI, jeden pro odesílání zpráv do obchodních partnerů (volá **EDI odeslat most**) a jeden pro příjem zpráv z obchodních partnerů (volá **EDI přijímat most**).

V rámci tohoto řešení most odeslat EDI odpovídá na straně odesílání smlouvy a slouží k odesílání faktur EDIFACT ze společnosti Contoso na Northwind. Podobně most receive EDI odpovídá na straně příjmu smlouvy a slouží k přijímání potvrzení z databáze Northwind.  

### <a name="create-the-trading-partners"></a>Vytvořit obchodní partneři
Začněte tím vytvořte obchodní partneři pro společnosti Contoso a Northwind.  

1. V portálu BizTalk Services na **partneři** klikněte na tlačítko **přidat**.
2. Na nové stránce partnera, zadejte **Contoso** jako název partnera s identitou a pak klikněte na tlačítko **Uložit**.
3. Opakujte krok k vytvoření druhého partnera **Northwind**.  

### <a name="create-the-agreement"></a>Vytvoření smlouvy
Smluv s obchodními partnery se vytvoří mezi profily obchodní obchodními partnery. Toto řešení používá výchozí profily partnerů, které automaticky vytvářejí, když jsme vytvořili partnerů.  

1. Na portálu BizTalk Services, klikněte na tlačítko **smlouvy** > **přidat**.
2. V nové smlouvy **obecné nastavení** stránky, zadejte hodnoty, jak je znázorněno na následujícím obrázku a potom klikněte na tlačítko **pokračovat**.
   
   ![][3]  
   
   Po kliknutí na **pokračovat**, dvě karty **přijímat nastavení** a **odeslat nastavení** budou k dispozici.
3. Vytvoření smlouvy odesílání mezi Contoso a Northwind. Tato smlouva řídí, jak společnosti Contoso odešle faktur EDIFACT Northwind.
   
   1. Klikněte na tlačítko **nastavení odesílání**.
   2. Zachovat na výchozí hodnoty **příchozí URL**, **transformace**, a **dávkování** karty.
   3. Na **protokol** ve skupině **schémata** části tím, že nahrajete **EFACT_D93A_INVOIC.xsd** schématu. Toto schéma je k dispozici s ukázkového balíčku.
      
      ![][4]  
   4. Na **přenosu** kartu, zadejte podrobnosti pro fronty služby Service Bus. Straně odesílání smlouvy se používá **northwindreceive** frontu pro odesílání faktur EDIFACT Northwind a **pozastaveno** fronty směrovat všechny zprávy, které během zpracování selhat a jsou pozastavena. Vytvoří tyto fronty v **krok 1: vytvoření fronty služby Service Bus** (v tomto tématu).
      
      ![][5]  
      
      V části **nastavení přenosu > typ přenosu** a **pozastavení se vaše nastavení zpráv > typ přenosu**vyberte Azure Service Bus a zadejte hodnoty, jak je znázorněno na obrázku.
4. Vytvoření přijmout smlouvu mezi společností Contoso a Northwind. Tato smlouva řídí, jak společnosti Contoso přijme potvrzení z databáze Northwind.
   
   1. Klikněte na tlačítko **obdrží nastavení**.
   2. Zachovat na výchozí hodnoty **přenosu** a **transformace** karty.
   3. Na **protokol** ve skupině **schémata** části tím, že nahrajete **EFACT_4.1_CONTRL.xsd** schématu. Toto schéma je k dispozici s ukázkového balíčku.
   4. Na **trasy** kartu, vytvořit filtr k zajištění, že pouze potvrzení z databáze Northwind jsou směrovány na Contoso. V části **trasy nastavení**, klikněte na tlačítko **přidat** vytvořit směrovací filtr.
      
      ![][6]  
      
      1. Zadejte hodnoty pro **název pravidla**, **trasy pravidlo**, a **trasy cílové** jak je znázorněno na obrázku.
      2. Klikněte na **Uložit**.
   5. Na **trasy** kartu znovu, zadejte, kde jsou směrovány pozastavené potvrzení (potvrzení, které nesplní během zpracování). Nastavit typ přenosu do služby Azure Service Bus, směrovat do cílového typu **fronty**, typ ověřování pro **sdílený přístupový podpis** (SAS), zadejte připojovací řetězec SAS pro obor názvů služby Service Bus a pak zadejte název fronty jako **pozastaveno**.
5. Nakonec klikněte na tlačítko **nasadit** nasazení smlouvy. Všimněte si, koncové body kde odesílání a příjem smlouvy nasadí.
   
   * Na **odeslat nastavení** ve skupině **příchozí URL**, Všimněte si, koncový bod. Odeslat zprávu ze společnosti Contoso na Northwind pomocí bridge odeslat EDI, musíte odeslat zprávu na tento koncový bod.
   * Na **přijímat nastavení** ve skupině **přenosu**, Všimněte si, koncový bod. Odeslat zprávu z databáze Northwind pro Contoso pomocí EDI přijímat most, je nutné odeslat zprávu na tento koncový bod.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Krok 3: Vytvoření a nasazení projektu služby BizTalk Services
V předchozím kroku nasazené odeslat EDI a přijímat smluv a zpracování faktur EDIFACT a potvrzení. Tyto smlouvy můžou jenom zpracování zpráv, které odpovídají standardní schématu zprávy EDIFACT. Ale na scénář pro toto řešení, Contoso odešle faktury Northwind v interních proprietární schématu. Tedy před odesláním zprávy k odeslání mostu EDI, ho musí transformuje ze schématu pracovišti na standardní schéma faktur EDIFACT. BizTalk Services EAI project sleduje, která.

BizTalk Services projektu **InvoiceProcessingBridge**, transformací zprávy je také zahrnut jako součást vzorku, který jste stáhli. Projekt obsahuje následující artefakty:

* **INHOUSEINVOICE. XSD** – schématu pracovišti faktury, které je odesláno Northwind.
* **EFACT_D93A_INVOIC. XSD** – schéma standardní faktur EDIFACT.
* **EFACT_4.1_CONTRL. XSD** – schéma potvrzení EDIFACT, která Northwind odesílá do společnosti Contoso.
* **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – transformaci, která se mapuje na standardní schéma faktur EDIFACT schématu pracovišti faktury.  

### <a name="create-the-biztalk-services-project"></a>Vytvoření projektu služby BizTalk Services
1. V řešení sady Visual Studio rozbalte projekt InvoiceProcessingBridge a pak otevřete **MessageFlowItinerary.bcs** souboru.
2. Klikněte na libovolné místo na plátně a nastavit **adresy URL služby BizTalk** v poli vlastnosti zadat název vašeho odběru služby BizTalk Services. Například, `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. Z panelu nástrojů přetáhněte **Xml One-Way most** na plátno. Nastavte **název Entity** a **relativní adresu** vlastnosti spojení s **ProcessInvoiceBridge**. Dvakrát klikněte na panel **ProcessInvoiceBridge** otevřete povrchu konfigurace mostu.
4. V rámci **typy zpráv** pole, klikněte na znaménko plus (**+**) tlačítko a zadejte schéma příchozí zprávy. Vzhledem k tomu, že příchozí zpráva pro přemostění EAI, je vždy interní faktury, nastavte na **INHOUSEINVOICE**.
   
   ![][8]  
5. Klikněte na tlačítko **transformaci Xml** tvaru a v dialogovém okně Vlastnosti pro **mapy** vlastnosti, klikněte na tlačítko se třemi tečkami (**...** ) tlačítko. V **mapy výběr** dialogové okno, vyberte **INHOUSEINVOICE_to_D93AINVOIC** transformačního souboru a pak klikněte na tlačítko **OK**.
   
   ![][9]  
6. Přejděte zpět na **MessageFlowItinerary.bcs**a přetáhněte z panelu nástrojů **obousměrný externí koncový bod služby** napravo od **ProcessInvoiceBridge**. Nastavte jeho **název Entity** vlastnost **EDIBridge**.
7. V Průzkumníku řešení rozbalte **MessageFlowItinerary.bcs** a dvakrát klikněte **EDIBridge.config** souboru. Nahradí obsah **EDIBridge.config** následujícím kódem.
   
   > [!NOTE]
   > Proč je potřeba upravit soubor .config? Koncový bod externí služby, kterou jsme přidali do plátna návrháře most představuje mosty EDI, které jsme předtím nasadili. Přemostění EDI jsou obousměrné mosty s odeslání a na straně příjmu. Přemostění EAI, přidaný do návrháře most ale jednosměrné most. Ano pro zpracování různých zpráv exchange vzorcům dva mosty, používáme mostu vlastní chování včetně jeho konfiguraci v souboru .config. Kromě toho vlastní chování také zpracovávat ověřování ke koncovému bodu most odeslat EDI. Toto vlastní chování je k dispozici jako samostatné ukázce kódu na [BizTalk Services most řetězení ukázkový – EAI k EDI](https://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Toto řešení znovu použije vzorek.  
   > 
   > 
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <system.serviceModel>
       <extensions>
         <behaviorExtensions>
           <add name="BridgeAuthentication" 
                 type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
         </behaviorExtensions>
       </extensions>
       <behaviors>
         <endpointBehaviors>
           <behavior name="BridgeAuthenticationConfiguration">
             <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
             <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                   issuername="owner" 
                                   issuersecret="[YOUR ACS SECRET]" />
             <webHttp />
           </behavior>
         </endpointBehaviors>
       </behaviors>
       <bindings>
         <webHttpBinding>
           <binding name="BridgeBindingConfiguration">
             <security mode="Transport" />
           </binding>
         </webHttpBinding>
       </bindings>
       <client>
         <clear />
         <!--
           Go BizTalk Portal > Agreement > Send Settings > Inbound URL
           Copy the Endpoint URL and paste it in the below address field
         -->
         <endpoint name="TwoWayExternalServiceEndpointReference1" 
                   address="[YOUR EDI BRIDGE SEND URI]" 
                   behaviorConfiguration="BridgeAuthenticationConfiguration" 
                   binding="webHttpBinding" 
                   bindingConfiguration="BridgeBindingConfiguration" 
                   contract="System.ServiceModel.Routing.IRequestReplyRouter" />
       </client>
     </system.serviceModel>
   </configuration>
   
   ```
8. Aktualizace souboru EDIBridge.config podrobnosti o konfiguraci
   
   * V části *<behaviors>*, zadejte obor názvů služby ACS a klíč spojený s předplatným BizTalk Services.
   * V části *<client>*, zadejte koncový bod, ve kterém je nasazená odeslání smlouvy EDI.
   
   Uložte změny a zavřete soubor konfigurace.
9. Z panelu nástrojů klikněte na tlačítko **konektor** a připojte se k **ProcessInvoiceBridge** a **EDIBridge** komponenty. Vyberte konektor a nastavte vlastnosti pole **podmínky filtru** k **všechny shody**. Tím se zajistí, že všechny zprávy zpracované přemostění EAI, jsou směrovány na most EDI.
   
   ![][10]  
10. Uložte změny do řešení.  

### <a name="deploy-the-project"></a>Nasazení projektu
1. Na počítači, ve které jste vytvořili projekt služby BizTalk Services stáhněte a nainstalujte certifikát SSL pro vaše předplatné služby BizTalk Services. Z v rámci služby BizTalk Services, klikněte na tlačítko **řídicí panel**a potom klikněte na tlačítko **stáhnout certifikát SSL**. Dvakrát klikněte na certifikát a po zobrazení výzvy k dokončení instalace. Ujistěte se, že nainstalujete certifikát v části **důvěryhodných kořenových certifikačních autorit** úložiště certifikátů.
2. V Průzkumníku řešení Visual Studio, klikněte pravým tlačítkem myši **InvoiceProcessingBridge** projektu a pak klikněte na tlačítko **nasadit**.
3. Zadejte hodnoty, jak je znázorněno na obrázku a potom klikněte na tlačítko **nasadit**. Přihlašovací údaje služby ACS pro služby BizTalk můžete získat kliknutím **informace o připojení** z řídicího panelu služby BizTalk Services.
   
   ![][11]  
   
   V podokně výstup zkopírovat koncového bodu, které přemostění EAI se nasadí, třeba `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Tuto adresu URL koncového bodu budete potřebovat později.  

## <a name="step-4-test-the-solution"></a>Krok 4: Testování řešení
V tomto tématu se podíváme na to, jak pomocí otestování řešení **kurzu klienta** aplikace poskytuje jako součást vzorku.  

1. V sadě Visual Studio, stiskněte klávesu F5 pro spuštění **kurzu klienta**.
2. Na obrazovce musí mít hodnoty předem z kroku, kde jsme vytvořili fronty služby Service Bus. Klikněte na **Další**.
3. V dalším okně zadejte přihlašovací údaje služby ACS pro předplatné služby BizTalk Services a koncových bodů kde EAI a EDI (přijímání) jsou nasazeny přemostění.
   
   Koncový bod přemostění EAI měl zkopírovali v předchozím kroku. EDI zasláno most koncový bod, portálu BizTalk Services, přejděte na Smlouvu > Zobrazit nastavení > přenosu > koncový bod.
   
   ![][12]  
4. V dalším okně pod Contoso, klikněte **odeslat interní faktury** tlačítko. V souboru otevřete dialogové okno, otevřete soubor INHOUSEINVOICE.txt. Zkontrolujte obsah souboru a pak klikněte na tlačítko **OK** odesílat faktury.
   
   ![][13]  
5. Během několika sekund faktury doručen Northwind. Klikněte na tlačítko **zobrazit zprávu** odkaz zobrazíte faktury přijatých Northwind. Všimněte si, jak faktury přijatých Northwind je ve standardním EDIFACT schématu při odeslaným společností Contoso schématu pracovišti.
   
   ![][14]  
6. Vyberte faktury a potom klikněte na tlačítko **odeslání potvrzení**. V dialogovém okně, která se otevře Všimněte si, že ID výměny stejné v přijaté faktury a potvrzení odeslání. Klikněte na tlačítko OK v **odeslání potvrzení** dialogové okno.
   
   ![][15]  
7. Během několika sekund se potvrzení úspěšně přijme ve společnosti Contoso.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Krok 5 (volitelné): Odeslat EDIFACT faktury v dávkách
BizTalk Services EDI přemostění také podporuje dávkování odchozích zpráv. Tato funkce je užitečná pro příjem partnery, kteří dávají přednost dávku zpráv (splňující určité kritérium) namísto jednotlivých zpráv.

Nejdůležitější aspekty při práci s dávky je skutečná verze služby batch, také nazývané kritéria uvolnění. Kritéria uvolnění můžou týkat jak přijímajícího partnera chce, aby se pro příjem zpráv. Pokud je povolené dávkování, EDI most neodesílá odchozí zprávy do přijímajícího partnera dokud není splněna kritéria uvolnění. Například dávkování kritéria na základě odešle zprávu velikost dávky pouze tehdy, když n zprávy jsou dávce. Kritéria batch může být také založené na čase tak, aby dávky je odeslána na pevně stanovený čas každý den. V tomto řešení se snažíme velikost zprávy na základě kritérií.

1. Na portálu BizTalk Services klikněte na tlačítko smlouvou, kterou jste vytvořili dříve. Klikněte na tlačítko Odeslat Nastavení > dávkování > Přidat služby Batch.
2. Název dávky, zadejte **InvoiceBatch**, zadejte popis a pak klikněte na tlačítko **Další**.
3. Zadejte kritéria služby batch, definující, které zprávy musí být dávce. V tomto řešení jsme dávkové všechny zprávy. Ano, vyberte použít rozšířené možnosti definice a zadejte **1 = 1**. Je to podmínku, která bude mít vždy hodnotu true, a proto všechny zprávy se mají přidat do dávky. Klikněte na **Další**.
   
   ![][17]  
4. Zadejte kritéria uvolnění dávky. Z rozevíracího seznamu vyberte **MessageCountBased**a pro **počet**, zadejte **3**. To znamená, že dávku tři zprávy se odešlou do Northwind. Klikněte na **Další**.
   
   ![][18]  
5. Zkontrolujte souhrn a pak klikněte na tlačítko **Uložit**. Klikněte na tlačítko **nasadit** k opětovnému nasazení smlouvy.
6. Přejděte zpět **kurzu klienta**, klikněte na tlačítko **odeslat interní faktury**a postupujte podle výzev k odeslání faktury. Můžete si všimnout, že žádné faktury neobdrží v Northwind, protože velikost dávky není splněná. Opakujte tento krok ještě dvakrát, takže budete mít tři faktury zprávy odeslané do Northwind. To splňuje kritéria uvolnění dávky 3 zpráv a měli byste vidět faktury v Northwind.

<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

