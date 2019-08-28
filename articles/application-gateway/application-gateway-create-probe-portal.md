---
title: Vytvoření vlastního testu – Azure Application Gateway – Azure Portal | Microsoft Docs
description: Zjistěte, jak vytvořit vlastní test pro Application Gateway pomocí portálu.
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: b92b9d953b6dd941b8b5f445ad64059f557c2980
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061788"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Vytvoření vlastního testu pro Application Gateway pomocí portálu

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

V tomto článku přidáte vlastní test do existující aplikační brány prostřednictvím Azure Portal. Vlastní sondy jsou užitečné pro aplikace, které mají konkrétní stránku kontroly stavu nebo pro aplikace, které neposkytují úspěšnou odpověď na výchozí webovou aplikaci.

## <a name="before-you-begin"></a>Před zahájením

Pokud ještě nemáte Aplikační bránu, přečtěte si téma [vytvoření Application Gateway](application-gateway-create-gateway-portal.md) a vytvořte tak Aplikační bránu, se kterou chcete pracovat.

## <a name="createprobe"></a>Vytvoření testu paměti

Testy se konfigurují v procesu se dvěma kroky prostřednictvím portálu. Prvním krokem je vytvoření testu. V druhém kroku přidáte test do nastavení http back-endu služby Application Gateway.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Pokud ještě nemáte účet, můžete si zaregistrovat [bezplatnou měsíční zkušební verzi](https://azure.microsoft.com/free) .

1. V podokně Oblíbené Azure Portal klikněte na všechny prostředky. V okně všechny prostředky klikněte na Aplikační brána. Pokud předplatné, které jste vybrali, již obsahuje několik prostředků, můžete zadat partners.contoso.net do pole filtrovat podle názvu... pro snadný přístup ke službě Application Gateway.

1. Klikněte na sondy a kliknutím na tlačítko **Přidat** přidejte test.

   ![Okno Přidat test paměti s vyplněnými informacemi][1]

1. V okně **Přidat sondu stavu** vyplňte požadované informace pro test a po dokončení klikněte na tlačítko **OK**.

   |**Nastavení** | **Hodnota** | **Podrobnosti**|
   |---|---|---|
   |**Název**|customProbe|Tato hodnota je popisný název sondy, která je přístupná na portálu.|
   |**Protokol**|HTTP nebo HTTPS | Protokol, který sonda stavu používá.|
   |**Hostitel**|t contoso.com|Tato hodnota je název hostitele, který se používá pro test paměti. Dá se použít jenom v případě, že je na Application Gateway nakonfigurovaný vícenásobný web, jinak použijte 127.0.0.1. Tato hodnota se liší od názvu hostitele virtuálního počítače.|
   |**Cesta**|nebo jiná cesta|Zbytek úplné adresy URL pro vlastní test paměti. Platná cesta začíná znakem/. Pro výchozí cestu http:\//contoso.com stačí použít '/'. |
   |**Interval (s)**|30|Jak často je sonda spuštěná, aby kontrolovala stav. Nedoporučuje se nastavit méně než 30 sekund.|
   |**Časový limit (sekundy)**|30|Doba, po kterou sonda čeká před vypršením časového limitu. Interval časového limitu musí být dostatečně vysoký, aby bylo možné provést volání http, aby bylo zajištěno, že je stránka stavu back-endu k dispozici.|
   |**Prahová hodnota špatného stavu**|3|Počet neúspěšných pokusů, které se považují za chybné. Prahová hodnota může být nastavena na 1 nebo více.|

   > [!IMPORTANT]
   > Název hostitele není stejný jako název serveru. Tato hodnota je název virtuálního hostitele spuštěného na aplikačním serveru. Sonda se pošle http://(název hostitele):(portu z httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Přidání testu do brány

Teď, když je test vytvořený, je čas ho přidat do brány. Nastavení sondy se nastavuje v nastavení http back-endu služby Application Gateway.

1. Kliknutím na **Nastavení http** v bráně Application Gateway otevřete okno Konfigurace a klikněte na aktuální nastavení back-endu http uvedená v okně.

   ![okno nastavení https][2]

1. V okně nastavení **appGatewayBackEndHttpSettings** zaškrtněte políčko **použít vlastní test paměti** a v rozevíracím seznamu [vytvořit test](#createprobe) paměti vyberte test paměti vytvořený v části **vlastní test paměti** .
   Po dokončení klikněte na **Uložit** a nastavení se použije.

Výchozí test kontroluje výchozí přístup k webové aplikaci. Teď, když se vytvořil vlastní test paměti, používá Aplikační brána vlastní cestu definovanou k monitorování stavu back-end serverů. Na základě definovaných kritérií ověří služba Application Gateway cestu určenou v testu. Pokud volání hosta: port/cesta nevrátí stavovou odpověď HTTP 200-399, server se po dosažení prahové hodnoty "není v pořádku" převezme. Zjišťování pokračuje v instanci, která není v pořádku, aby bylo možné určit, kdy bude opět v pořádku. Až se instance přidá zpátky do fondu serverů v pořádku, provoz se znovu přesměruje na tento stav a zjišťování instance pokračuje v intervalu zadaného uživatelem jako normální.

## <a name="next-steps"></a>Další postup

Informace o tom, jak nakonfigurovat snižování zátěže SSL pomocí Azure Application Gateway, najdete v tématu [konfigurace přesměrování zpracování SSL](application-gateway-ssl-portal.md) .

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

