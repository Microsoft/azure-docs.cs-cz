---
title: Vytvoření řadiče dat ARC Azure v Azure Portal
description: Vytvoření řadiče dat ARC Azure v Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: 12d0997e677bcca423f32951e99a6202855104ad
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030862"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Vytvoření řadiče dat ARC Azure v Azure Portal

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>Úvod

Pomocí Azure Portal můžete vytvořit řadič dat ARC Azure.

Mnohé z možností vytváření pro Azure Arc se spouští v Azure Portal, i když je prostředek, který se má vytvořit nebo spravovat, mimo infrastrukturu Azure. Vzor uživatelského prostředí v těchto případech, zejména v případě, že mezi Azure a vaším prostředím nedochází k přímému připojení, je použít Azure Portal k vygenerování skriptu, který pak můžete stáhnout a spustit ve vašem prostředí, abyste navázali zabezpečené připojení zpátky do Azure. Například servery s podporou ARC Azure při [vytváření serverů s podporou ARC](../servers/onboard-portal.md)budou postupovat podle tohoto vzoru.

Když použijete režim nepřímých připojení pro datové služby s podporou ARC Azure, můžete použít Azure Portal k vygenerování poznámkového bloku, který pak můžete stáhnout a spustit v Azure Data Studio proti vašemu clusteru Kubernetes. 

Když použijete režim přímého připojení, můžete zřídit řadič dat přímo z Azure Portal. Další informace o [režimech připojení](connectivity.md)si můžete přečíst.

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Použití Azure Portal k vytvoření kontroleru dat ARC Azure

Pomocí následujících kroků vytvořte řadič dat ARC Azure pomocí Azure Portal a Azure Data Studio.

1. Nejdřív se přihlaste k [webu Azure Portal Marketplace](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller).  Výsledky hledání na webu Marketplace se vyfiltrují, aby se zobrazila možnost řadič dat ARC Azure.
1. Pokud první krok nezadal kritéria hledání. Zadejte prosím do výsledků hledání, klikněte na Azure ARC data Controller.
1. Vyberte dlaždici Azure Data Controller z webu Marketplace.
1. Klikněte na tlačítko **vytvořit** .
1. Vyberte režim nepřímých připojení. Přečtěte si další informace o [režimech připojení a požadavcích](./connectivity.md). 
1. Přečtěte si požadavky pro vytvoření kontroleru dat ARC Azure a nainstalujte libovolný chybějící software, například Azure Data Studio a kubectl.
1. Klikněte na tlačítko **Další: podrobnosti řadiče dat** .
1. Vyberte předplatné, skupinu prostředků a umístění Azure stejným způsobem jako u jakýchkoli jiných prostředků, které vytvoříte v Azure Portal. V takovém případě bude umístění Azure, které vyberete, místo, kde se budou ukládat metadata o prostředku.  Samotný prostředek se vytvoří na jakékoli zvolené infrastruktuře. Nemusí být v infrastruktuře Azure.
1. Zadejte název vašeho kontroleru dat.

1. Vyberte konfigurační profil nasazení.
1. Klikněte na tlačítko **otevřít v Azure Studio** .
1. Na další obrazovce se zobrazí souhrn vašich výběrů a poznámkového bloku, který se vygeneruje.  Poznámkový blok si můžete stáhnout kliknutím na tlačítko **Stáhnout zřizovací Poznámkový blok** .

   > [!IMPORTANT]
   > Na platformě Azure Red Hat OpenShift nebo Red Hat OpenShift Container Platform musíte před vytvořením kontroleru dat použít omezení kontextu zabezpečení. Postupujte podle pokynů v tématu [použití omezení kontextu zabezpečení pro datové služby s podporou ARC Azure na OpenShift](how-to-apply-security-context-constraint.md).

1. Otevřete Poznámkový blok v Azure Data Studio a klikněte na tlačítko **Spustit vše** v horní části.
1. Dokončete vytvoření řadiče dat podle pokynů a pokynů v poznámkovém bloku.

## <a name="monitoring-the-creation-status"></a>Monitorování stavu vytváření

Dokončení vytvoření kontroleru bude trvat několik minut. Průběh můžete sledovat v jiném okně terminálu pomocí následujících příkazů:

> [!NOTE]
>  V ukázkových příkazech se předpokládá, že jste vytvořili řadič dat a obor názvů Kubernetes s názvem "ARC".  Pokud jste použili jiný název oboru názvů nebo data Controller, můžete "oblouk" nahradit vaším jménem.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Stav vytvoření kteréhokoli z nich můžete také ověřit spuštěním příkazu podobného níže.  To je užitečné hlavně při řešení problémů.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Řešení potíží s vytvářením

Pokud narazíte na potíže s vytvářením, přečtěte si [příručku Poradce při potížích](troubleshoot-guide.md).