---
title: Vložit klienta nástroje Internet Analyzer | Microsoft Docs
description: V tomto článku se dozvíte, jak do aplikace vložit klienta služby Internet Analyzer JavaScript.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f9ecb8d731945847160b49c68c554fafdd7285d9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "74896383"
---
# <a name="embed-the-internet-analyzer-client"></a>Vložit klienta nástroje Internet Analyzer

V tomto článku se dozvíte, jak vložit klienta JavaScriptu do aplikace. Instalace tohoto klienta je nutná pro spouštění testů a příjem analýz scorecardu. **Klient jazyka JavaScript specifický pro profil je k dispozici po nakonfigurování prvního testu.** Odtud můžete pokračovat v přidávání a odebírání testů do tohoto profilu, aniž byste museli vkládat nový skript. Další informace o nástroji Internet Analyzer najdete v [přehledu](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Před zahájením

Internet Analyzer vyžaduje přístup k Azure a dalším službám společnosti Microsoft, aby fungovaly správně. Před vložením klienta prosím `fpc.msedge.net` povolte přístup k síti a všechny předem nakonfigurované adresy URL koncového bodu (viditelné prostřednictvím rozhraní příkazového [řádku](internet-analyzer-cli.md)).

## <a name="find-the-client-script-url"></a>Najít adresu URL klientského skriptu

Adresu URL skriptu můžete najít buď prostřednictvím Azure Portal, nebo prostřednictvím rozhraní příkazového řádku Azure CLI po dokončení konfigurace testu. Další informace najdete v tématu [vytvoření prostředku nástroje Internet Analyzer](internet-analyzer-create-test-portal.md).

Možnost 1. V Azure Portal pomocí [tohoto odkazu](https://aka.ms/InternetAnalyzerPreviewPortal) otevřete stránku portálu Preview pro Azure Internet Analyzer. Přejděte na svůj profil Internet Analyzer a zobrazte adresu URL skriptu tak, že přejdete na **nastavení > konfigurace**.

Možnost 2. Pomocí Azure CLI ověřte `scriptFileUri` vlastnost.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Podrobnosti o klientovi

Skript je vygenerován speciálně pro váš profil a testy. Po načtení se skript spustí s prodlevou 2 sekundy. Nejprve kontaktuje službu Internet Analyzer, aby načetla seznam koncových bodů konfigurovaných ve vašich testech. Potom spustí měření a nahraje časované výsledky zpátky do služby Internet Analyzer.

## <a name="client-examples"></a>Příklady klientů

Tyto příklady znázorňují několik základních metod, jak vložit klientský JavaScript do vaší webové stránky nebo aplikace. Pro adresu `0bfcb32638b44927935b9df86dcfe397` URL skriptu používáme jako příklad ID profilu.

### <a name="run-on-page-load"></a>Spustit při načtení stránky
Nejjednodušším způsobem je použít značku skriptu uvnitř bloku značek Meta. Tato značka spustí skript jedenkrát za načtení stránky.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k Internet Analyzer](internet-analyzer-faq.md)
