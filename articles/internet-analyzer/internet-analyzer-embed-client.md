---
title: Vložit klienta analyzátoru internetu | Dokumenty společnosti Microsoft
description: V tomto článku se dozvíte, jak vložit klienta JavaScript u nástroje Internet Analyzer do aplikace.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f9ecb8d731945847160b49c68c554fafdd7285d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74896383"
---
# <a name="embed-the-internet-analyzer-client"></a>Vložení klienta Nástroje pro analýzu Internetu

Tento článek ukazuje, jak vložit klienta JavaScriptu do aplikace. Instalace tohoto klienta je nezbytná pro spuštění testů a příjem analýzy přehledu výkonnostních metrik. **Klient JavaScriptu specifický pro profil je k dispozici po konfiguraci prvního testu.** Odtud můžete pokračovat v přidávání nebo odebírat testy do tohoto profilu bez nutnosti vložit nový skript. Další informace o nástroji Internet Analyzer naleznete v [přehledu](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Než začnete

Internet Analyzer vyžaduje přístup k Azure a dalším službám Microsoftu, aby fungoval správně. Před vložením `fpc.msedge.net` klienta povolte přístup k adresám URL koncového bodu a všem předkonfigurovaným adresám URL koncového bodu (viditelným prostřednictvím [cli).](internet-analyzer-cli.md)

## <a name="find-the-client-script-url"></a>Vyhledání adresy URL klientského skriptu

Adresu URL skriptu najdete buď prostřednictvím portálu Azure nebo azure cli po nakonfigurovaný test. Další informace naleznete [v tématu Create a Internet Analyzer Resource](internet-analyzer-create-test-portal.md).

Možnost 1. Na webu Azure Portal použijte [tento odkaz](https://aka.ms/InternetAnalyzerPreviewPortal) k otevření stránky portálu preview pro Azure Internet Analyzer. Přejděte do profilu nástroje Internet Analyzer a zobceji adresu URL skriptu v **nastavení > konfigurace**.

Možnost 2. Pomocí azure CLI, `scriptFileUri` zkontrolujte vlastnost.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Podrobnosti o klientovi

Skript je generován speciálně pro váš profil a testy. Po načtení se skript spustí se zpožděním o 2 sekundy. Nejprve kontaktuje službu Internet Analyzer a načte seznam koncových bodů nakonfigurovaných v testech. Potom spustí měření a nahraje časované výsledky zpět do služby Internet Analyzer.

## <a name="client-examples"></a>Příklady klientů

Tyto příklady ukazují několik základních metod pro vložení klientského JavaScriptu do webové stránky nebo aplikace. Používáme `0bfcb32638b44927935b9df86dcfe397` jako příklad ID profilu pro adresu URL skriptu.

### <a name="run-on-page-load"></a>Spustit při načítání stránky
Nejjednodušší metodou je použití značky skriptu uvnitř bloku metaznačky. Tato značka spustí skript jednou za načtení stránky.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Další kroky

Přečtěte si [nejčastější dotazy k analyzátoru Internetu](internet-analyzer-faq.md)
