---
title: Vytvoření vlastní sondy pomocí portálu
titleSuffix: Azure Application Gateway
description: Zjistěte, jak vytvořit vlastní sondu pro aplikační bránu pomocí portálu
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74074612"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Vytvoření vlastní sondy pro aplikační bránu pomocí portálu

> [!div class="op_single_selector"]
> * [Portál Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

V tomto článku přidáte vlastní sondu stavu do existující aplikační brány prostřednictvím portálu Azure. Pomocí sond stavu Azure Application Gateway monitoruje stav prostředků ve fondu back-endu.

## <a name="before-you-begin"></a>Než začnete

Pokud ještě nemáte aplikační bránu, navštivte [stránku Vytvoření aplikační brány](application-gateway-create-gateway-portal.md) a vytvořte aplikační bránu, se kterou chcete pracovat.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Vytvořit sondu pro aplikační bránu v2 skladové položky

Sondy jsou konfigurovány ve dvoustupňovém procesu prostřednictvím portálu. Prvním krokem je zadání hodnot požadovaných pro konfiguraci sondy. V druhém kroku otestujete stav back-endu pomocí této konfigurace sondy a uložte sondu. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Zadat vlastnosti sondy

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Pokud ještě nemáte účet, můžete si zaregistrovat [bezplatnou měsíční zkušební verzi](https://azure.microsoft.com/free)

2. Na webu Azure Portal v podokně Oblíbené klikněte na Všechny prostředky. Klikněte na bránu aplikace v okně Všechny prostředky. Pokud předplatné, které jste vybrali, již obsahovalo nějaké prostředky, můžete zadat partners.contoso.net do pole Filtrovat podle názvu... pro snadný přístup ke službě Application Gateway.

3. Vyberte **sondy stavu** a pak vyberte **Přidat,** chcete-li přidat novou sondu stavu.

   ![Přidat novou sondu][4]

4. Na stránce **Přidat sondu stavu** vyplňte požadované informace pro sondu a po dokončení vyberte **OK**.

   |**Nastavení** | **Hodnotu** | **Podrobnosti**|
   |---|---|---|
   |**Název**|customProbe|Tato hodnota je popisný název pro sondu, která je přístupná na portálu.|
   |**Protokol**|PROTOKOL HTTP nebo HTTPS | Protokol, který používá sonda stavu. |
   |**Hostitel**|Tj contoso.com|Tato hodnota je název virtuálního hostitele (odlišný od názvu hostitele virtuálního počítače) spuštěného na aplikačním serveru. Sonda je odeslána do (protocol)://(název hostitele):(port z httpsetting)/urlPath.  To platí, když je v application gateway nakonfigurováno více lokalit. Pokud je aplikační brána nakonfigurována pro jednu lokalitu, zadejte "127.0.0.1".|
   |**Vybrat název hostitele z nastavení http back-endu**|Ano nebo Ne|Nastaví záhlaví *hostitele* v sondě na název hostitele back-endového prostředku v back-endovém fondu přidruženém k nastavení HTTP, ke kterému je tato sonda přidružena. Speciálně požadované v případě víceklientských backendů, jako je například služba aplikace Azure. [Další informace](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Cesta**|/ nebo jiná cesta|Zbývající část úplné adresy URL pro vlastní sondu. Platná cesta začíná na '/'. Pro výchozí cestu http:\//contoso.com stačí použít '/' |
   |**Interval (sekundy)**|30|Jak často je sonda spuštěna ke kontrole stavu. Nedoporučuje se nastavit nižší než 30 sekund.|
   |**Časový čas (sekundy)**|30|Doba, po kterou sonda čeká před vypršením časového limitu. Pokud není přijata platná odpověď v tomto časovém období, sonda je označena jako neúspěšná. Časový interval musí být dostatečně vysoká, že volání http lze provést k zajištění back-endu stav stránky je k dispozici. Všimněte si, že hodnota časového intervalu by neměla být větší než hodnota Interval použitá v tomto nastavení sondy nebo hodnota "Časový interval požadavku" v nastavení HTTP, která bude přidružena k této sondě.|
|**Prahová hodnota pro poškozený stav**|3|Počet po sobě jdoucích neúspěšných pokusů, které mají být považovány za nefunkční. Prahovou hodnotu lze nastavit na hodnotu 1 nebo více.|
   |**Použít podmínky shody sondy**|Ano nebo Ne|Ve výchozím nastavení je odpověď HTTP(S) se stavovým kódem mezi 200 a 399 považována za v pořádku. Můžete změnit přijatelný rozsah kódu back-endové odpovědi nebo těla odpovědi back-endu. [Další informace](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**Nastavení HTTP**|výběr z rozevíracího seznamu|Sonda bude přidružena k nastavení http, které je zde vybráno, a proto bude sledovat stav tohoto back-endového fondu, který je přidružen k vybranému nastavení HTTP. Bude používat stejný port pro požadavek sondy jako ten, který se používá ve vybraném nastavení HTTP. Můžete zvolit pouze nastavení HTTP, které nejsou přidruženy k žádné jiné vlastní sondy. <br>Všimněte si, že pouze tato nastavení HTTP jsou k dispozici pro přidružení, které mají stejný protokol jako protokol zvolený v této konfiguraci sondy a mají stejný stav pro přepínač *nastavení HTTP vyskladnění hostitele z back-endu.*|
   
   > [!IMPORTANT]
   > Sonda bude sledovat stav back-endu pouze v případě, že je přidružena k jednomu nebo více nastavením PROTOKOLU HTTP. Bude sledovat back-endové prostředky těchto back-endových fondů, které jsou přidruženy k nastavení http, ke kterému je tato sonda přidružena. Požadavek na sondu bude odeslán na adresu http://(název hostitele):(port z httpsetting)/urlPath.

### <a name="test-backend-health-with-the-probe"></a>Otestujte stav back-endu se sondou

Po zadání vlastností sondy můžete otestovat stav prostředků back-endu a ověřit, zda je konfigurace sondy správná a že prostředky back-endu fungují podle očekávání.

1. Vyberte **Test** a poznamenejte si výsledek sondy. Aplikační brána testuje stav všech back-endových prostředků v back-endových fondech přidružených k nastavení http používá pro tuto sondu. 

   ![Test stavu back-endu][5]

2. Pokud existují nějaké prostředky back-endu není v pořádku, zkontrolujte **podrobnosti** sloupec pochopit důvod není v pořádku stavu prostředku. Pokud byl prostředek označen jako nefunkční z důvodu nesprávné konfigurace sondy, vyberte odkaz **Přejít zpět na sondu** a upravte konfiguraci sondy. V opačném případě pokud byl prostředek označen jako nefunkční z důvodu problému s back-endem, vyřešte problémy s back-endovým prostředkem a potom znovu otestujte back-end výběrem odkazu **Přejít zpět na sondu** a vyberte **testovat**.

   > [!NOTE]
   > Můžete uložit sondu i s prostředky back-endu není v pořádku, ale nedoporučuje se. Důvodem je, že brána aplikace odebere tyto back-endové prostředky z back-endového fondu, které jsou určeny jako není v pořádku promoru. V případě, že neexistují žádné prostředky v pořádku v back-endu fondu, nebudete mít přístup k vaší aplikaci a zobrazí se chyba 502.

   ![Zobrazit výsledek sondy][6]

3. Vyberte **Přidat,** chcete-li sondu uložit. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Vytvořit sondu pro aplikační bránu v1 skladové položky

Sondy jsou konfigurovány ve dvoustupňovém procesu prostřednictvím portálu. Prvním krokem je vytvoření sondy. V druhém kroku přidáte sondu do nastavení http back-endu aplikační brány.

### <a name="create-the-probe"></a><a name="createprobe"></a>Vytvoření sondy

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Pokud ještě nemáte účet, můžete si zaregistrovat [bezplatnou měsíční zkušební verzi](https://azure.microsoft.com/free)

2. Na webu Azure Portal v podokně Oblíbené vyberte **Všechny prostředky**. Vyberte bránu aplikace na stránce **Všechny prostředky.** Pokud předplatné, které jste vybrali, již obsahovalo nějaké prostředky, můžete zadat partners.contoso.net do pole Filtrovat podle názvu... pro snadný přístup ke službě Application Gateway.

3. Vyberte **Sondy** a pak vyberte **Přidat,** chcete-li přidat sondu.

   ![Přidat okno sondy s vyplněnými informacemi][1]

4. V okně **Přidat sondu stavu** vyplňte požadované informace pro sondu a po dokončení vyberte **OK**.

   |**Nastavení** | **Hodnotu** | **Podrobnosti**|
   |---|---|---|
   |**Název**|customProbe|Tato hodnota je popisný název pro sondu, která je přístupná na portálu.|
   |**Protokol**|PROTOKOL HTTP nebo HTTPS | Protokol, který používá sonda stavu. |
   |**Hostitel**|Tj contoso.com|Tato hodnota je název virtuálního hostitele (odlišný od názvu hostitele virtuálního počítače) spuštěného na aplikačním serveru. Sonda je odeslána do (protocol)://(název hostitele):(port z httpsetting)/urlPath.  To platí, když je v application gateway nakonfigurováno více lokalit. Pokud je aplikační brána nakonfigurována pro jednu lokalitu, zadejte "127.0.0.1".|
   |**Vybrat název hostitele z nastavení http back-endu**|Ano nebo Ne|Nastaví záhlaví *hostitele* v sondě na název hostitele back-endového prostředku v back-endovém fondu přidruženém k nastavení HTTP, ke kterému je tato sonda přidružena. Speciálně požadované v případě víceklientských backendů, jako je například služba aplikace Azure. [Další informace](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Cesta**|/ nebo jiná cesta|Zbývající část úplné adresy URL pro vlastní sondu. Platná cesta začíná na '/'. Pro výchozí cestu http:\//contoso.com stačí použít '/' |
   |**Interval (sekundy)**|30|Jak často je sonda spuštěna ke kontrole stavu. Nedoporučuje se nastavit nižší než 30 sekund.|
   |**Časový čas (sekundy)**|30|Doba, po kterou sonda čeká před vypršením časového limitu. Pokud není přijata platná odpověď v tomto časovém období, sonda je označena jako neúspěšná. Časový interval musí být dostatečně vysoká, že volání http lze provést k zajištění back-endu stav stránky je k dispozici. Všimněte si, že hodnota časového intervalu by neměla být větší než hodnota Interval použitá v tomto nastavení sondy nebo hodnota "Časový interval požadavku" v nastavení HTTP, která bude přidružena k této sondě.|
|**Prahová hodnota pro poškozený stav**|3|Počet po sobě jdoucích neúspěšných pokusů, které mají být považovány za nefunkční. Prahovou hodnotu lze nastavit na hodnotu 1 nebo více.|
   |**Použít podmínky shody sondy**|Ano nebo Ne|Ve výchozím nastavení je odpověď HTTP(S) se stavovým kódem mezi 200 a 399 považována za v pořádku. Můžete změnit přijatelný rozsah kódu back-endové odpovědi nebo těla odpovědi back-endu. [Další informace](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > Název hostitele není stejný jako název serveru. Tato hodnota je název virtuálního hostitele spuštěného na aplikačním serveru. Sonda je odeslána do http://(název hostitele):(port z httpsetting)/urlPath

### <a name="add-probe-to-the-gateway"></a>Přidání sondy do brány

Nyní, když byla sonda vytvořena, je čas ji přidat do brány. Nastavení sondy jsou nastavena v nastavení http back-endu aplikační brány.

1. Chcete-li připojit konfigurační okno, klepněte na **tlačítko Nastavení http** v aplikaci a klepnutím na tlačítko Nastavení http uvedené v okně klepněte na nastavení http back-endu.

   ![okno nastavení https][2]

2. Na stránce nastavení **appGatewayBackEndHttpSettings** zaškrtněte políčko **Použít vlastní sondu** a zvolte sondu vytvořenou v části Vytvořit [sondu](#createprobe) v rozevíracím seznamu **Vlastní sonda.**
   Po dokončení klikněte na **Uložit** a nastavení se použijí.

## <a name="next-steps"></a>Další kroky

Zobrazení stavu prostředků back-endu určené sonda pomocí [zobrazení stavu back-endu](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
