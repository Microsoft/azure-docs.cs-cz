---
title: Vytvoření vlastního testu pomocí portálu
titleSuffix: Azure Application Gateway
description: Zjistěte, jak vytvořit vlastní test pro Application Gateway pomocí portálu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 5d2760415e4f4ef3b181f2fb69802659fec3ef66
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95975951"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Vytvoření vlastního testu pro Application Gateway pomocí portálu

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

V tomto článku přidáte pomocí Azure Portal vlastní sondu stavu do existující aplikační brány. Pomocí sond stavu Azure Application Gateway monitoruje stav prostředků ve fondu back-end.

## <a name="before-you-begin"></a>Než začnete

Pokud ještě nemáte Aplikační bránu, přečtěte si téma [vytvoření Application Gateway](./quick-create-portal.md) a vytvořte tak Aplikační bránu, se kterou chcete pracovat.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Vytvoření testu pro SKU Application Gateway v2

Testy se konfigurují v procesu se dvěma kroky prostřednictvím portálu. Prvním krokem je zadání hodnot požadovaných pro konfiguraci sondy. V druhém kroku otestujete stav back-endu pomocí této konfigurace testu a uložíte sondu. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Zadat vlastnosti testu paměti

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Pokud ještě nemáte účet, můžete si zaregistrovat [bezplatnou měsíční zkušební verzi](https://azure.microsoft.com/free) .

2. Na webu Azure Portal v podokně Oblíbené klikněte na Všechny prostředky. V okně všechny prostředky klikněte na Aplikační brána. Pokud předplatné, které jste vybrali, již obsahovalo nějaké prostředky, můžete zadat partners.contoso.net do pole Filtrovat podle názvu... pro snadný přístup ke službě Application Gateway.

3. Vyberte **sondy stavu** a pak výběrem **Přidat** přidejte novou sondu stavu.

   ![Přidat nový test paměti][4]

4. Na stránce **Přidat sondu stavu** vyplňte požadované informace pro test a po dokončení vyberte **OK**.

   |**Nastavení** | **Hodnota** | **Podrobnosti**|
   |---|---|---|
   |**Název**|customProbe|Tato hodnota je popisný název, který je dán pro sondu, která je přístupná na portálu.|
   |**Protokol**|HTTP nebo HTTPS | Protokol, který sonda stavu používá. |
   |**Hostitel**|t contoso.com|Tato hodnota je název virtuálního hostitele (liší se od názvu hostitele virtuálního počítače) běžícího na aplikačním serveru. Sonda se posílá do \<protocol\> :// \<host name\> :\<port\>/\<urlPath\>|
   |**Vyberte název hostitele z nastavení HTTP back-endu.**|Ano nebo Ne|Nastaví hlavičku *hostitele* v testu na název hostitele z nastavení protokolu HTTP, ke kterému je tento test přidružen. Speciálně vyžadované v případě back-endu s více klienty, jako je například Azure App Service. [Další informace](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Vybrat port z nastavení HTTP back-endu**| Ano nebo Ne|Nastaví *port* sondy stavu na port z nastavení protokolu HTTP, ke kterému je tento test přidružen. Pokud zvolíte Ne, můžete zadat vlastní cílový port, který se má použít. |
   |**Port**| 1-65535 | Vlastní port, který se má použít pro sondy stavu | 
   |**Cesta**|nebo libovolná platná cesta|Zbytek úplné adresy URL pro vlastní test paměti. Platná cesta začíná znakem/. Pro výchozí cestu http: \/ /contoso.com stačí použít '/'. |
   |**Interval (s)**|30|Jak často je sonda spuštěná, aby kontrolovala stav. Nedoporučuje se nastavit méně než 30 sekund.|
   |**Časový limit (sekundy)**|30|Doba, po kterou sonda čeká před vypršením časového limitu. Pokud v tomto časovém limitu neobdrží platná odpověď, sonda je označena jako neúspěšná. Interval časového limitu musí být dostatečně vysoký, aby bylo možné provést volání http, aby bylo zajištěno, že je stránka stavu back-endu k dispozici. Všimněte si, že hodnota časového limitu by neměla být větší než hodnota intervalu použitá v tomto nastavení testu nebo hodnota časový limit požadavku v nastavení HTTP, které bude přidruženo k této sondě.|
   |**Prahová hodnota pro poškozený stav**|3|Počet po sobě jdoucích neúspěšných pokusů, které se mají považovat za chybné. Prahová hodnota může být nastavena na 1 nebo více.|
   |**Použít podmínky pro porovnání sondy**|Ano nebo Ne|Ve výchozím nastavení je odpověď HTTP (S) se stavovým kódem mezi 200 a 399 považována za v pořádku. Můžete změnit přijatelný rozsah kódu odpovědi na back-end nebo text odpovědi na back-end. [Další informace](./application-gateway-probe-overview.md#probe-matching)|
   |**Nastavení HTTP**|výběr z rozevíracího seznamu|Sonda se bude přidružit k vybraným nastavením HTTP a proto bude monitorovat stav tohoto fondu back-end, který je přidružen k vybranému nastavení protokolu HTTP. Bude používat stejný port pro požadavek sondy jako ten, který se používá ve vybraném nastavení protokolu HTTP. Můžete zvolit jenom nastavení HTTP, která nejsou přidružená k žádné jiné vlastní sondy. <br>Všimněte si, že pro přidružení jsou k dispozici pouze nastavení HTTP, která mají stejný protokol jako protokol vybraný v této konfiguraci testu, a mají stejný stav pro *název hostitele výběru z přepínače nastavení http back-endu* .|
   
   > [!IMPORTANT]
   > Sonda bude monitorovat stav back-endu pouze v případě, že je přidružena k jednomu nebo více nastavením protokolu HTTP (y). Bude monitorovat záložní prostředky těchto fondů back-end, které jsou přidruženy k nastavení HTTP, ke kterým je tento senzor přidružen. Požadavek sondy se odešle jako \<protocol\> :// \<hostName\> : \<port\> / \<urlPath\> .

### <a name="test-backend-health-with-the-probe"></a>Testování stavu back-endu pomocí testu paměti

Po zadání vlastností sondy můžete otestovat stav záložních prostředků, abyste ověřili, že je konfigurace sondy správná a že prostředky back-endu fungují podle očekávání.

1. Vyberte **test** a poznamenejte si výsledek testu. Application Gateway testuje stav všech back-end prostředků ve fondech back-endu přidružených k nastavením HTTP, které se používá pro tento test. 

   ![Stav testovacího back-endu][5]

2. Pokud nějaké prostředky back-endu nejsou v pořádku, zkontrolujte sloupec **podrobností** a pochopte důvod stavu prostředku, který není v pořádku. Pokud je prostředek označený jako špatný v důsledku nesprávné konfigurace testu paměti, vyberte odkaz **Přejít zpět na test paměti** a upravte konfiguraci sondy. V opačném případě, pokud je prostředek označený jako špatný v důsledku problému s back-end, vyřešte problémy s back-end prostředkem a pak back-end otestujte znovu tak, že vyberete odkaz **Přejít zpět na** test a vyberete **test**.

   > [!NOTE]
   > Můžete si vybrat, že se má test uložit i v případě nesprávného back-endu prostředků, ale nedoporučuje se to. Důvodem je to, že Application Gateway nepředává požadavky na back-end servery ze služby back-end, které jsou zjišťovány k tomu, že sonda není v pořádku. V případě, že ve fondu back-end nejsou žádné dobré prostředky, nebudete mít přístup k aplikaci a zobrazí se chyba HTTP 502.

   ![Zobrazit výsledek testu][6]

3. Vyberte **Přidat** a uložte sondu. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Vytvořit test pro SKU Application Gateway v1

Testy se konfigurují v procesu se dvěma kroky prostřednictvím portálu. Prvním krokem je vytvoření testu. V druhém kroku přidáte test do nastavení http back-endu služby Application Gateway.

### <a name="create-the-probe"></a><a name="createprobe"></a>Vytvoření testu paměti

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Pokud ještě nemáte účet, můžete si zaregistrovat [bezplatnou měsíční zkušební verzi](https://azure.microsoft.com/free) .

2. Na webu Azure Portal v podokně Oblíbené vyberte **Všechny prostředky**. Na stránce **všechny prostředky** vyberte Aplikační brána. Pokud předplatné, které jste vybrali, již obsahovalo nějaké prostředky, můžete zadat partners.contoso.net do pole Filtrovat podle názvu... pro snadný přístup ke službě Application Gateway.

3. Vyberte **sondy** a pak vyberte **Přidat** a přidejte sondu.

   ![Okno Přidat test paměti s vyplněnými informacemi][1]

4. V okně **Přidat sondu stavu** vyplňte požadované informace pro test a po dokončení vyberte **OK**.

   |**Nastavení** | **Hodnota** | **Podrobnosti**|
   |---|---|---|
   |**Název**|customProbe|Tato hodnota je popisný název, který je dán pro sondu, která je přístupná na portálu.|
   |**Protokol**|HTTP nebo HTTPS | Protokol, který sonda stavu používá. |
   |**Hostitel**|t contoso.com|Tato hodnota je název virtuálního hostitele (liší se od názvu hostitele virtuálního počítače) běžícího na aplikačním serveru. Sonda se posílá do (protokol)://(název hostitele):(portu z httpsetting)/urlPath.  To platí v případě, že je na Application Gateway nakonfigurovaný vícenásobný Web. Pokud je Application Gateway nakonfigurované pro jednu lokalitu, zadejte "127.0.0.1".|
   |**Vyberte název hostitele z nastavení HTTP back-endu.**|Ano nebo Ne|Nastaví hlavičku *hostitele* v testu na název hostitele back-endu prostředku v back-endové skupině přidružené k nastavení HTTP, ke kterému je tento test přidružen. Speciálně vyžadované v případě back-endu s více klienty, jako je například Azure App Service. [Další informace](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**Cesta**|nebo libovolná platná cesta|Zbytek úplné adresy URL pro vlastní test paměti. Platná cesta začíná znakem/. Pro výchozí cestu http: \/ /contoso.com stačí použít '/'. |
   |**Interval (s)**|30|Jak často je sonda spuštěná, aby kontrolovala stav. Nedoporučuje se nastavit méně než 30 sekund.|
   |**Časový limit (sekundy)**|30|Doba, po kterou sonda čeká před vypršením časového limitu. Pokud v tomto časovém limitu neobdrží platná odpověď, sonda je označena jako neúspěšná. Interval časového limitu musí být dostatečně vysoký, aby bylo možné provést volání http, aby bylo zajištěno, že je stránka stavu back-endu k dispozici. Všimněte si, že hodnota časového limitu by neměla být větší než hodnota intervalu použitá v tomto nastavení testu nebo hodnota časový limit požadavku v nastavení HTTP, které bude přidruženo k této sondě.|
   |**Prahová hodnota pro poškozený stav**|3|Počet po sobě jdoucích neúspěšných pokusů, které se mají považovat za chybné. Prahová hodnota může být nastavena na 1 nebo více.|
   |**Použít podmínky pro porovnání sondy**|Ano nebo Ne|Ve výchozím nastavení je odpověď HTTP (S) se stavovým kódem mezi 200 a 399 považována za v pořádku. Můžete změnit přijatelný rozsah kódu odpovědi na back-end nebo text odpovědi na back-end. [Další informace](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > Název hostitele není stejný jako název serveru. Tato hodnota je název virtuálního hostitele spuštěného na aplikačním serveru. Sonda se posílá do \<protocol\> :// \<hostName\> :\<port from http settings\>/\<urlPath\>

### <a name="add-probe-to-the-gateway"></a>Přidání testu do brány

Teď, když je test vytvořený, je čas ho přidat do brány. Nastavení sondy se nastavuje v nastavení http back-endu služby Application Gateway.

1. Kliknutím na **Nastavení http** v bráně Application Gateway otevřete okno Konfigurace a klikněte na aktuální nastavení back-endu http uvedená v okně.

   ![okno nastavení https][2]

2. Na stránce nastavení **appGatewayBackEndHttpSettings** zaškrtněte políčko **použít vlastní test paměti** a vyberte test, který jste vytvořili v části [Vytvoření sondy](#createprobe) v rozevíracím seznamu **vlastní test paměti** .
   Po dokončení klikněte na **Uložit** a nastavení se použije.

## <a name="next-steps"></a>Další kroky

Prohlédněte si stav prostředků back-end podle určení sondou pomocí [zobrazení stavu back-endu](./application-gateway-diagnostics.md#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
