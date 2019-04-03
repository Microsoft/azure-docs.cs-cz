---
title: Vytvoření vlastního testu paměti – Azure Application Gateway – Azure Portal | Dokumentace Microsoftu
description: Informace o vytvoření vlastního testu paměti pro službu Application Gateway pomocí portálu
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
ms.openlocfilehash: 90d576fd00a39f7e871cbe0922ce131dfbe38ff0
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862161"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Vytvoření vlastního testu paměti pro službu Application Gateway pomocí portálu

> [!div class="op_single_selector"]
> * [portál Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

V tomto článku přidáte vlastní test paměti existující aplikační bráně pomocí webu Azure portal. Vlastní sondy jsou užitečné pro aplikace, které mají konkrétní stránky kontroly stavu nebo pro aplikace, které neposkytují úspěšné odpovědi na výchozí webovou aplikaci.

## <a name="before-you-begin"></a>Před zahájením

Pokud již nemáte služby application gateway, navštivte [vytvoření služby Application Gateway](application-gateway-create-gateway-portal.md) k vytvoření služby application gateway pro práci s.

## <a name="createprobe"></a>Vytvořit test paměti

Testy jsou nakonfigurované ve dvou krocích prostřednictvím portálu. Prvním krokem je vytvoření sondy. V druhém kroku přidat test do nastavení http back-endu služby application gateway.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Pokud ještě nemáte účet, můžete si zaregistrovat [zkušební verze na měsíc zdarma](https://azure.microsoft.com/free)

1. V podokně Azure portal Oblíbené klikněte na možnost všechny prostředky. Klikněte na tlačítko application gateway v okně všechny prostředky. Pokud předplatné, které jste vybrali, již má několik prostředků, můžete zadat partners.contoso.net do pole filtrovat podle názvu... pro snadný přístup ke službě Application Gateway.

1. Klikněte na tlačítko **sondy** a klikněte na tlačítko **přidat** tlačítko pro přidání testu.

   ![Přidat test okno s informacemi o vyplnění][1]

1. Na **přidat sondu stavu** okno, vyplňte požadované informace pro kontrolu a po dokončení klikněte na tlačítko **OK**.

   |**Nastavení** | **Hodnota** | **Podrobnosti**|
   |---|---|---|
   |**Název**|customProbe|Tato hodnota je popisný název, který test, který je přístupný na portálu.|
   |**Protocol (Protokol)**|Protokol HTTP nebo HTTPS | Protokol, který používá sondy stavu.|
   |**Hostitel**|i.e contoso.com|Tato hodnota je název hostitele, který se používá pro test paměti. Použít pouze v případě více webů je nakonfigurovaná ve službě Application Gateway, v opačném případě použijte "127.0.0.1". Tato hodnota se liší od názvu hostitele virtuálního počítače.|
   |**Cesta**|/ nebo jinou cestu|Zbývající část úplnou adresu url pro vlastní test paměti. Platná cesta začínající znakem "/". Pro výchozí cestu k protokolu http:\//contoso.com použijte "/" |
   |**Interval (sekundy)**|30|Jak často je ke kontrole stavu spuštění testu. Nedoporučuje se nastavit nižší než 30 sekund.|
   |**Časový limit (sekundy)**|30|Množství času, které čeká, než vyprší časový limit testu. Interval časového limitu musí být dostatečně vysoká, volání protokolu http lze zajistit, že je k dispozici na stránce stavu back-endu.|
   |**Prahová hodnota pro poškozený stav**|3|Počet neúspěšných pokusů o se považoval za poškozený. Prahová hodnota 0 znamená, že okamžitě určen není v pořádku, pokud selže kontrola stavu back endu.|

   > [!IMPORTANT]
   > Název hostitele není stejný jako název serveru. Tato hodnota je název virtuálního hostitele se spuštěnou na aplikačním serveru. Sonda se odesílá do http://(host name):(port from httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Přidat test do brány

Teď, když test paměti se vytvořil, je čas ho přidat do brány. Test nastavení v nastavení http back-endu služby application gateway.

1. Klikněte na tlačítko **nastavení HTTP** ve službě application gateway, abyste vyvolali okně klikněte na konfigurace aktuální back-endového nastavení http uvedené v okně.

   ![okno nastavení protokolu HTTPS][2]

1. Na **appGatewayBackEndHttpSettings** okno nastavení, zaškrtněte **použít vlastní test paměti** zaškrtávací políčko a zvolte test vytvořit v [vytvoření sondy](#createprobe) věnované  **Vlastní test paměti** rozevíracího seznamu...
   Jakmile budete hotovi, klikněte na tlačítko **Uložit** a nastavení se použijí.

Výchozí kontroly kontroluje výchozí přístup k webové aplikaci. Teď, když vytvoření vlastního testu paměti application gateway používá vlastní cestu definovanou pro sledování stavu pro back-end serverů. Application gateway na základě kritérií, která byla definována, zkontroluje cestě zadané v testu. Pokud volání hostitele: Port / cestu nevrací HTTP 200-399 stav odpovědi, server je vyřazen ze smyčky po dosažení prahová hodnota špatného stavu. Zjišťování bude pokračovat na instanci není v pořádku, můžete určit, kdy je opět v pořádku. Po instance je přidána zpět do fondu serverů v pořádku, začne provoz směřující do ho znovu a testování instance pokračuje v zadaném intervalu uživatele jako za normálních okolností.

## <a name="next-steps"></a>Další postup

Další konfigurace přesměrování zpracování SSL pomocí Azure Application Gateway najdete v tématu [konfigurovat přesměrování zpracování SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

