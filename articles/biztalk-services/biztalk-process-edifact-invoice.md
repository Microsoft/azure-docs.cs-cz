---
title: 'Kurz: Zpracovat EDIFACT faktury pomocí služby Azure BizTalk Services | Microsoft Docs'
description: Jak vytvořit a nakonfigurovat konektor pole nebo rozhraní API app a použít ho v aplikaci logiky ve službě Azure App Service
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
ms.openlocfilehash: 2ebd6a8cb70f218c3b56bc78c9b853dbf51ab468
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26633862"
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Kurz: Proces EDIFACT faktury pomocí služby Azure BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Na portálu služby BizTalk můžete použít ke konfiguraci a nasazení X12 a smluv EDIFACT. V tomto kurzu podíváme na tom, jak vytvořit smlouvy EDIFACT pro výměnu faktury mezi obchodními partnery. Tento kurz je napsán kolem začátku do konce obchodní řešení zahrnující dva obchodní partneři Northwind a Contoso, které exchange EDIFACT zprávy.  

## <a name="sample-based-on-this-tutorial"></a>Ukázka podle tohoto kurzu
Tento kurz je napsán kolem ukázku, **odesílání EDIFACT faktury pomocí BizTalk Services**, která je k dispozici ke stažení z [galerie kódů MSDN](http://go.microsoft.com/fwlink/?LinkId=401005). Můžete použít ukázkový a projít tento kurz a pochopit, jak byl vytvořený v ukázkovém. Nebo můžete použít k vytvoření vlastního řešení základů tohoto kurzu. V tomto kurzu je zaměřena na druhém postupu tak, že chápete, jak byl sestaven toto řešení. Také možné míře, tento kurz je konzistentní s ukázkou a používá stejné názvy artefakty (například schémat, transformací) jako použité v ukázce.  

> [!NOTE]
> Protože toto řešení zahrnuje odesílání zprávy ze EAI most do mostu EDI, znovu použije [most služby BizTalk řetězení ukázka](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) ukázka.  
> 
> 

## <a name="what-does-the-solution-do"></a>Jakým způsobem řešení?
V tomto řešení Northwind obdrží EDIFACT faktury ze společnosti Contoso. Tyto faktury nejsou ve standardním formátu EDI. Ano před odesláním faktury Northwind, se musí transformován do dokument EDIFACT faktura (také nazývané INVOIC). Na přijetí musí Northwind zpracovat EDIFACT faktury a obnoví řídicí zprávu (také nazývané CONTRL) Contoso.

![][1]  

K dosažení této podnikový scénář, společnost Contoso využívá funkce poskytované službou Microsoft Azure BizTalk Services.

* Společnost Contoso využívá mosty EAI na transformaci původní fakturu EDIFACT INVOIC.
* Most EAI pak odešle zprávu do mostu odesílání EDI nasazen jako součást smlouvu nakonfigurované na portálu služby BizTalk.
* Most odesílání EDI zpracovává EDIFACT INVOIC a směruje na Northwind.
* Po přijetí faktury, vrátí Northwind CONTRL zprávy k EDI přijímat most? víc informací, které jsou nasazeny jako součást této smlouvy.  

> [!NOTE]
> Volitelně můžete toto řešení také ukazuje, jak pomocí dávkování odesílat faktur v dávkách, místo abyste odesílali každé faktury samostatně.  
> 
> 

Chcete-li dokončit tento scénář, jsme používat fronty Service Bus odesílat faktury ze společnosti Contoso Northwind nebo přijímat potvrzení z Northwind. Tyto fronty si můžete vytvořit pomocí klientské aplikace, která je k dispozici ke stažení a je součástí balíčku vzorku, který je k dispozici v rámci tohoto kurzu.  

## <a name="prerequisites"></a>Požadavky
* Musí mít obor názvů sběrnice. Pokyny pro vytvoření oboru názvů, naleznete v části [postupy: vytvoření nebo úprava Namespace služby Service Bus](https://msdn.microsoft.com/library/azure/hh674478.aspx). Předpokládejme, že už máte obor názvů sběrnice zřízený, nazývá **edifactbts**.
* Musíte mít předplatné služby BizTalk Services. V tomto kurzu, dejte nám předpokládá, že máte předplatné služby BizTalk, názvem **contosowabs**.
* Zaregistrujte své předplatné služby BizTalk Services na portálu služby BizTalk. Pokyny najdete v tématu [registrace nasazení služby BizTalk na portálu služby BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)
* Musíte mít nainstalovanou sadu Visual Studio.
* Musíte mít nainstalované sady SDK služby BizTalk. Si můžete stáhnout sady SDK z [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Krok 1: Vytvoření fronty Service Bus
Toto řešení používá fronty Service Bus pro výměnu zpráv mezi obchodními partnery. Contoso a Northwind odesílání zpráv do front z kde mosty EAI a EDI využívat. Pro toto řešení je třeba tři fronty Service Bus:

* **northwindreceive** – Northwind přijme faktury z Contoso přes tuto frontu.
* **contosoreceive** – Contoso přijme potvrzení z Northwind přes tuto frontu.
* **pozastaveno** – všechny pozastavené zprávy jsou směrovány do této fronty. Zprávy jsou pozastavené, pokud se nepodaří během zpracování.

Tyto fronty Service Bus můžete vytvořit pomocí součástí balíčku vzorku klientskou aplikaci.  

1. Z umístění, kam jste stáhli ukázku, otevřete **kurzu odesílání faktury pomocí BizTalk Services EDI Bridges.sln**.
2. Stiskněte klávesu **F5** sestavíte a spustíte **kurzu klienta** aplikace.
3. V dialogovém okně zadejte obor názvů Service Bus ACS, název vystavitele a klíč vystavitele.
   
   ![][2]  
4. Okno se zprávou vyzve, aby tři fronty budou vytvořeny v oboru názvů sběrnice. Klikněte na **OK**.
5. Ponechte kurzu klienty v provozu. Otevřít, klikněte na tlačítko **Service Bus** > ***vašeho oboru názvů Service Bus*** > **fronty**a ověřte, že jsou vytvořeny tři fronty.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Krok 2: Vytvoření a nasazení smlouvy obchodním partnerem
Vytvoření smlouvy s obchodním partnerem mezi Contoso a Northwind. Smlouvy s obchodním partnerem definuje kontrakt obchodu mezi dvěma obchodními partnery, jako je například které zpráva schématu pro použití, které zasílání zpráv protokolu chcete použít, atd. Smlouvy s obchodním partnerem obsahuje dva mosty EDI, jeden k odesílání zpráv do obchodních partnerů (volat **EDI odeslat most**) a jeden pro příjem zpráv z obchodních partnerů (volat **EDI přijímat most**).

V kontextu tohoto řešení se most odesílání EDI odpovídá straně odesílání smlouvy a se používá k odeslání do Northwind faktury EDIFACT ze společnosti Contoso. Podobně most receive EDI odpovídá na straně příjmu smlouvy a slouží k potvrzení příjmu z Northwind.  

### <a name="create-the-trading-partners"></a>Vytvoření obchodními partnery
Pro společnosti Contoso a Northwind začínat, vytvořte obchodní partneři.  

1. Na portálu služeb BizTalk na **partnery** , klikněte na **přidat**.
2. Na nové stránce partnera, zadejte **Contoso** jako název partnera a pak klikněte na tlačítko **Uložit**.
3. Opakujte krok k vytvoření druhého partnera **Northwind**.  

### <a name="create-the-agreement"></a>Vytvoření smlouvy
Smluv s obchodními partnery se vytvoří mezi profily obchodní obchodních partnerů. Toto řešení používá výchozí partnera profily, které se automaticky vytvoří, když jsme vytvořili partnery.  

1. Na portálu služby BizTalk, klikněte na tlačítko **smlouvy** > **přidat**.
2. V nové smlouvy **obecné nastavení** stránky, zadejte hodnoty, jak je znázorněno na obrázku níže a pak klikněte na tlačítko **pokračovat**.
   
   ![][3]  
   
   Po kliknutí na tlačítko **pokračovat**, dvě karty **přijímat nastavení** a **odeslat nastavení** k dispozici.
3. Vytvoření smlouvy odesílání mezi Contoso a Northwind. Tato smlouva řídí, jak Contoso odešle faktury EDIFACT Northwind.
   
   1. Klikněte na tlačítko **odeslat nastavení**.
   2. Zachovat výchozí hodnoty na **příchozí adresy URL**, **transformace**, a **Batching** karty.
   3. Na **protokol** v části **schémata** části tím, že nahrajete **EFACT_D93A_INVOIC.xsd** schématu. Toto schéma je k dispozici s balíčkem ukázka.
      
      ![][4]  
   4. Na **přenosu** kartě, zadejte podrobnosti pro fronty Service Bus. Smlouvy na straně odesílání používáme **northwindreceive** fronty k odeslání do Northwind, faktury EDIFACT a **pozastaveno** fronty směrovat všechny zprávy, které během zpracování selhat a jsou pozastavené. Můžete vytvořit tyto fronty v **krok 1: vytvoření fronty Service Bus** (v tomto tématu).
      
      ![][5]  
      
      V části **přenosu nastavení > typ přenosu** a **nastavení pozastavení zpráv > typ přenosu**vyberte Azure Service Bus a zadejte hodnoty, jak je znázorněno na obrázku.
4. Vytvořte receive smlouvu mezi společností Contoso a Northwind. Tato smlouva řídí, jak Contoso obdrží od Northwind potvrzení.
   
   1. Klikněte na tlačítko **obdrží nastavení**.
   2. Zachovat výchozí hodnoty na **přenosu** a **transformace** karty.
   3. Na **protokol** v části **schémata** části tím, že nahrajete **EFACT_4.1_CONTRL.xsd** schématu. Toto schéma je k dispozici s balíčkem ukázka.
   4. Na **trasy** kartě, vytvoření filtru k zajištění, že pouze potvrzení z Northwind jsou směrované na Contoso. V části **trasy nastavení**, klikněte na tlačítko **přidat** k vytvoření filtru rozhraní směrování.
      
      ![][6]  
      
      1. Zadejte hodnoty pro **název pravidla**, **trasy pravidlo**, a **trasy cílové** jak je znázorněno na obrázku.
      2. Klikněte na **Uložit**.
   5. Na **trasy** kartě znovu, zadejte, kde jsou směrovány pozastavenou potvrzení (potvrzení, které nesplní během zpracování). Nastavte typ přenosu k Azure Service Bus, směrovat cílový typ **fronty**, typ ověřování **sdíleného přístupového podpisu** (SAS), zadejte připojovací řetězec SAS pro obor názvů Service Bus a pak zadejte název fronty jako **pozastaveno**.
5. Nakonec klikněte na **nasadit** k nasazení této smlouvy. Všimněte si, koncových bodů kde odesílání a příjmu nasadí smlouvy.
   
   * Na **odeslat nastavení** v části **příchozí adresy URL**, Všimněte si, koncový bod. Pokud chcete odeslat zprávu ze společnosti Contoso Northwind pomocí most odesílání EDI, je nutné odeslat zprávu na tento koncový bod.
   * Na **přijímat nastavení** v části **přenosu**, Všimněte si, koncový bod. Chcete-li odeslat zprávu z Northwind do společnosti Contoso pomocí EDI obdržet most, je nutné odeslat zprávu na tento koncový bod.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Krok 3: Vytvoření a nasazení projektu služby BizTalk Services
V předchozím kroku nasadit EDI odesílat a přijímat smlouvy ke zpracování EDIFACT faktury a potvrzení. Tyto smlouvy můžou jen proces zprávy, které odpovídala standardní schématu EDIFACT zprávy. Ale na tento scénář pro toto řešení Contoso odešle faktury Northwind ve schématu vlastní interní. Ano předtím, než je zpráva odeslána do mostu odesílání EDI, ho musí transformaci z interních schématu standardní schématu faktury EDIFACT. BizTalk Services EAI projektu nemá který.

Projektu služby BizTalk Services **InvoiceProcessingBridge**, transformací zprávy je také zahrnuty jako součást vzorku, který jste stáhli. Tento projekt zahrnuje následující artefakty:

* **INHOUSEINVOICE. XSD** – schéma interní faktury, kterou posílá Northwind.
* **EFACT_D93A_INVOIC. XSD** – schéma standardní EDIFACT faktury.
* **EFACT_4.1_CONTRL. XSD** – schéma EDIFACT potvrzení, který odesílá Northwind Contoso.
* **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – transformace, která se mapuje na schéma standardní faktury EDIFACT schéma interní faktury.  

### <a name="create-the-biztalk-services-project"></a>Vytvoření projektu služby BizTalk Services
1. V řešení sady Visual Studio rozbalte InvoiceProcessingBridge projekt a pak otevřete **MessageFlowItinerary.bcs** souboru.
2. Klikněte kamkoli na plátně a nastavte **adresy URL služby BizTalk** v poli vlastnosti k určení předplatného název vaší služby BizTalk Services. Například, `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. Ze sady nástrojů, přetáhněte **Xml One-Way most** na plátno. Nastavte **název Entity** a **relativní adresu** vlastnosti mostu k **ProcessInvoiceBridge**. Klikněte dvakrát na **ProcessInvoiceBridge** otevřete prostor konfigurace most.
4. V rámci **typy zpráv** pole, klikněte na tlačítko plus (**+**) tlačítko zadejte schéma příchozí zprávy. Příchozí zpráva pro most EAI je vždy interní faktury, tuto možnost nastavíte na **INHOUSEINVOICE**.
   
   ![][8]  
5. Klikněte na tlačítko **transformace Xml** tvaru a v dialogovém okně Vlastnosti pro **mapy** vlastnosti, klikněte na tlačítko se třemi tečkami (**...** ) tlačítko. V **mapy výběr** dialogové okno, vyberte **INHOUSEINVOICE_to_D93AINVOIC** transformační soubor a potom klikněte na **OK**.
   
   ![][9]  
6. Přejděte zpět na **MessageFlowItinerary.bcs**a ze sady nástrojů, přetáhněte ji **obousměrný externí koncový bod služby** napravo **ProcessInvoiceBridge**. Nastavte její **název Entity** vlastnost **EDIBridge**.
7. V Průzkumníku řešení rozbalte **MessageFlowItinerary.bcs** a dvakrát klikněte na **EDIBridge.config** souboru. Nahradí obsah **EDIBridge.config** následujícím kódem.
   
   > [!NOTE]
   > Proč je potřeba upravit soubor .config? Externí služby koncového bodu, který jsme přidali do plátna návrháře most představuje mostů EDI, které jsme dříve nasadili. EDI mostů jsou obousměrný mostů s odesílání a na straně příjmu. Jednosměrné most je však EAI mostu, který jsme přidali do mostu návrháře. Ano pro zpracování vzory exchange jiná zpráva dvě mostů, používáme vlastní most chování včetně jeho konfiguraci v souboru config. Kromě toho vlastní chování také obstará ověřování ke koncovému bodu EDI odesílání most. Toto vlastní chování je k dispozici jako samostatné ukázka v [most služby BizTalk řetězení ukázka - EAI na EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Toto řešení opětovně používá vzorku.  
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
8. Aktualizace souboru EDIBridge.config podrobností konfigurace
   
   * V části  *<behaviors>* , zadejte obor názvů služby ACS a klíč spojený s předplatným služby BizTalk Services.
   * V části  *<client>* , poskytovat koncový bod, kde je nasazená smlouvy EDI odeslání.
   
   Uložte změny a zavřete konfiguračního souboru.
9. Z panelu nástrojů klikněte na tlačítko **konektor** a připojte **ProcessInvoiceBridge** a **EDIBridge** součásti. Vyberte konektor a v poli vlastnosti, nastavte **podmínku filtrování** k **všechny shody**. Tím se zajistí, že všechny zprávy, které jsou zpracovávány EAI most jsou směrovány do mostu EDI.
   
   ![][10]  
10. Uložte změny do řešení.  

### <a name="deploy-the-project"></a>Nasazení projektu
1. V počítači, kde jste vytvořili projektu služby BizTalk stáhněte a nainstalujte certifikát SSL pro vaše předplatné služby BizTalk Services. Klikněte v části služby BizTalk Services na **řídicí panel**a potom klikněte na **stáhnout certifikát SSL**. Dvakrát klikněte na certifikát a po zobrazení výzvy k dokončení instalace. Zajistěte, aby instalaci certifikátu v části **důvěryhodné kořenové certifikační autority** úložiště certifikátů.
2. V Průzkumníku řešení Visual Studio, klikněte pravým tlačítkem myši **InvoiceProcessingBridge** projektu a pak klikněte na **nasadit**.
3. Zadejte hodnoty, jak je znázorněno na obrázku a pak klikněte na tlačítko **nasadit**. Kliknutím můžete získat přihlašovací údaje služby ACS služby BizTalk Services **informace o připojení** na řídicím panelu služby BizTalk Services.
   
   ![][11]  
   
   V podokně výstup zkopírujte koncový bod, kde most EAI nasazení, například `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Tato adresa URL koncového bodu budete potřebovat později.  

## <a name="step-4-test-the-solution"></a>Krok 4: Test řešení
V tomto tématu se podíváme na tom, jak otestovat řešení pomocí **kurzu klienta** aplikace k dispozici jako součást vzorku.  

1. Ve Visual Studiu stisknutím klávesy F5 spusťte **kurzu klienta**.
2. Na obrazovce musí mít naplněnými z kroku hodnoty, které jsme vytvořili fronty Service Bus. Klikněte na **Další**.
3. V okně Další zadejte přihlašovací údaje služby ACS pro předplatné služby BizTalk Services a koncových bodů kde EAI a EDI (přijímat) jsou nasazeny mostů.
   
   Koncový bod most EAI měl zkopírovali v předchozím kroku. Pro EDI přijímat most koncovému bodu, na portálu služby BizTalk, přejděte na Smlouvu > přijímat Nastavení > přenosu > koncový bod.
   
   ![][12]  
4. V dalším okně pod Contoso, klikněte **odeslat interní faktury** tlačítko. V souboru otevřete dialogové okno, otevřete soubor INHOUSEINVOICE.txt. Zkontrolujte obsah souboru a pak klikněte na tlačítko **OK** Odeslat faktury.
   
   ![][13]  
5. Během pár sekund faktury přijme Northwind. Klikněte **zobrazení zpráv** odkaz zobrazíte faktury přijatých Northwind. Všimněte si, jak faktury přijatých Northwind je ve schématu standardní EDIFACT při jeden poslal společnosti Contoso interní schématu.
   
   ![][14]  
6. Vyberte fakturu a potom klikněte na **odeslat potvrzení**. V dialogovém okně, která se objeví Všimněte si, zda je ID výměnu stejné v přijatých faktury a potvrzení odesílána. Klikněte na tlačítko OK v **odeslat potvrzení** dialogové okno.
   
   ![][15]  
7. Během pár sekund se potvrzení úspěšně přijme společnosti Contoso.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Krok 5 (volitelné): faktury odeslat EDIFACT v dávkách
BizTalk Services EDI mostů podporuje také dávkování odchozích zpráv. Tato funkce je užitečná pro příjem partnery, které dávají přednost dávku zpráv (splňující určité kritérium) namísto jednotlivé zprávy.

Nejdůležitější aspekt při práci s dávky je skutečná verze služby batch, označované taky jako uvolnění kritéria. Kritéria verzi může být založen na jak přijímající partner chce dostávat zprávy. Pokud dávkování je povoleno, most EDI neodešle odchozí zprávy přijímající partnerovi dokud nebude splněna kritéria verze. Například dávkování kritéria na základě odešle zprávu velikost dávky pouze tehdy, když n zprávy jsou zpracovat v dávce. Kritéria batch může být také založené na čase, tak, aby dávky je odeslána v pevný čas každý den. V tomto řešení jsme zkuste velikost zprávy na základě kritérií.

1. Na portálu služby BizTalk klikněte na tlačítko smlouvy, kterou jste vytvořili dříve. Klikněte na tlačítko Nastavení odesílání > dávkování > Přidat Batch.
2. Název listu, zadejte **InvoiceBatch**, zadejte popis a pak klikněte na tlačítko **Další**.
3. Zadejte kritéria, dávky, která definuje, které zprávy musí být zpracovat v dávce. V tomto řešení jsme dávky všechny zprávy. Ano, vyberte použít rozšířené definice možnost a zadejte **1 = 1**. Toto je stav, který bude mít vždy hodnotu true, a proto všechny zprávy se zpracovat v dávce. Klikněte na **Další**.
   
   ![][17]  
4. Zadejte kritéria, verze batch. Vyberte z rozevíracího pole **MessageCountBased**a pro **počet**, zadejte **3**. To znamená, že dávku zpráv tři odešlou do Northwind. Klikněte na **Další**.
   
   ![][18]  
5. Zkontrolujte souhrn a pak klikněte na tlačítko **Uložit**. Klikněte na tlačítko **nasadit** znovu nasadit smlouvu.
6. Přejděte zpět na **kurzu klienta**, klikněte na tlačítko **odeslat interní faktury**a postupujte podle pokynů k odeslání faktury. Si všimnete, že žádné faktury je obdrželi v Northwind, protože velikost dávky není splněná. Tento krok opakujte dvakrát, takže budete mít tři faktury zprávy odeslané do Northwind. To splňuje kritéria batch verze 3 zprávy a měli byste vidět faktury v Northwind.

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

