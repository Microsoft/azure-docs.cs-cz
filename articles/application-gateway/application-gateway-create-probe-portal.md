---
title: Vytvoření vlastní test paměti - Azure Application Gateway - portálu Azure | Microsoft Docs
description: Naučte se vytvářet vlastní test paměti pro službu Application Gateway pomocí portálu
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
ms.openlocfilehash: 45737c1c378ec56a5e2bedec8c1f7b7bc7ba6225
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203903"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Vytvořit vlastní test paměti pro službu Application Gateway pomocí portálu

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

V tomto článku přidejte vlastní test paměti pro existující bránu aplikace prostřednictvím portálu Azure. Vlastní testy paměti jsou užitečné pro aplikace, které mají na stránce Kontrola konkrétní stav nebo pro aplikace, které neposkytuje úspěšné odpovědi na výchozí webovou aplikaci.

## <a name="before-you-begin"></a>Než začnete

Pokud již nemáte služby application gateway, navštivte [vytvoření služby Application Gateway](application-gateway-create-gateway-portal.md) k vytvoření aplikační brány pro práci s.

## <a name="createprobe"></a>Vytvoření sonda

Sondy jsou nakonfigurované ve dvou krocích prostřednictvím portálu. Prvním krokem je vytvoření sonda. V druhém kroku přidejte k kontroly nastavení http back-end aplikační brány.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com). Pokud účet nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/free)

1. V podokně Azure Oblíbené portálu klikněte na možnost všechny prostředky. Klikněte na službu application gateway v okně všechny prostředky. Pokud odběr, který jste již vybrali neobsahuje několik prostředků, můžete zadat partners.contoso.net ve filtru podle názvu... pro snadný přístup ke službě Application Gateway.

1. Klikněte na tlačítko **sondy** a klikněte na tlačítko **přidat** tlačítko, které chcete přidat sondu.

  ![Přidat test okno s informacemi o doplnit][1]

1. Na **test stavu přidat** okno, zadejte požadované informace pro kontrolu a po dokončení klikněte na tlačítko **OK**.

  |**Nastavení** | **Hodnota** | **Podrobnosti**|
  |---|---|---|
  |**Název**|customProbe|Tato hodnota je popisný název, který test, který je přístupný na portálu.|
  |**Protokol**|Protokol HTTP nebo HTTPS | Protokol, který používá test stavu.|
  |**Hostitel**|jednofaktorovému contoso.com|Tato hodnota je název hostitele, který se používá pro kontrolu. Platí jenom v případě více lokalit je nakonfigurovaná na aplikační bránu, v opačném případě použijte "127.0.0.1". Tato hodnota se liší od názvu hostitele virtuálního počítače.|
  |**Cesta**|/ nebo jiné cesty|Zbývající část úplnou adresu url pro vlastní test paměti. Platná cesta začíná '/'. Pro výchozí cestu http://contoso.com použijte '/' |
  |**Interval (sekundy)**|30|Jak často se spustí sondy, aby kontrolovat stav. Není doporučeno nastavit nižší než 30 sekund.|
  |**Časový limit (sekundy)**|30|Množství času sondy čeká, než vyprší časový limit. Interval vypršení časového limitu musí být dostatečně vysoká, že volání protokolu http lze zajistit, že stránka stavu back-end je k dispozici.|
  |**Prahová hodnota špatného stavu**|3|Počet neúspěšných pokusech o považovat za není v pořádku. Prahová hodnota 0 znamená, pokud se nezdaří Kontrola stavu back-end je určen není v pořádku okamžitě.|

  > [!IMPORTANT]
  > Název hostitele není stejný jako název serveru. Tato hodnota je název virtuálního hostitele na serveru aplikace spuštěna. Http://(host name):(port from httpsetting)/urlPath posílá sonda

## <a name="add-probe-to-the-gateway"></a>Přidat test k bráně.

Teď, když test byl vytvořen, je čas ji přidejte k bráně. Nastavení testu se nastavují v nastavení http back-end aplikační brány.

1. Klikněte na tlačítko **nastavení HTTP** ve službě application gateway se zprovoznit a klikněte na okno Konfigurace back-end aktuální nastavení http uvedené v okně.

  ![okno nastavení protokolu HTTPS][2]

1. Na **appGatewayBackEndHttpSettings** okno nastavení, zaškrtněte **použití vlastní test paměti** zaškrtávací políčko a zvolte testu vytvořené v [vytvořit sondy](#createprobe) části na **vlastní test** rozevíracího seznamu...
Po dokončení klikněte na tlačítko **Uložit** a nastavení se použijí.

Výchozí kontroly kontroluje výchozí úroveň přístupu k webové aplikaci. Teď, když vlastní test paměti byl vytvořen, služby application gateway používá vlastní cestu definovanou pro sledování stavu pro back-end serverů. Podle kritérií, která byla definována, služby application gateway kontroluje cesta zadaná v sonda. Pokud volání hostitele a portu / cesta nevrací HTTP 200 399 stav odpovědi, server se dostala mimo otočení po dosažení prahová hodnota špatného stavu. Zjišťování bude pokračovat na není v pořádku instance k určení, kdy bude znovu v pořádku. Po přidání instance zpět do fondu serverů v pořádku provoz začne znovu předávaných do něj a zjišťování k instanci pokračuje v zadaném intervalu uživatele jako normální.

## <a name="next-steps"></a>Další postup

Další postup konfigurace snižování zátěže protokolu SSL s Azure Application Gateway najdete v tématu [konfigurovat přesměrování zpracování SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

