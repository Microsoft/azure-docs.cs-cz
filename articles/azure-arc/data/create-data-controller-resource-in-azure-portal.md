---
title: Vytvoření řadiče dat ARC Azure v Azure Portal
description: Vytvoření řadiče dat ARC Azure v Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 9c928040aa2ff5a6ebfb7102c03450d3d7297b59
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686474"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Vytvoření řadiče dat ARC Azure v Azure Portal

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>Úvod

Pomocí Azure Portal můžete vytvořit řadič dat ARC Azure.

Mnohé z možností vytváření pro Azure Arc se spouští v Azure Portal, i když je prostředek, který se má vytvořit nebo spravovat, mimo infrastrukturu Azure. Vzor uživatelského prostředí v těchto případech, zejména v případě, že mezi Azure a vaším prostředím nedochází k přímému připojení, je použít Azure Portal k vygenerování skriptu, který pak můžete stáhnout a spustit ve vašem prostředí, abyste navázali zabezpečené připojení zpátky do Azure. Například servery s podporou ARC Azure při [vytváření serverů s podporou ARC](../servers/onboard-portal.md)budou postupovat podle tohoto vzoru.

V důsledku toho, že verze Preview podporuje pouze nepřímý připojený režim datových služeb s podporou ARC Azure, můžete použít Azure Portal k vygenerování poznámkového bloku, který je pak možné stáhnout a spustit v Azure Data Studio proti vašemu clusteru Kubernetes. Pokud je v budoucnu k dispozici přímo připojený režim, budete moci zřídit řadič dat přímo z Azure Portal. Další informace o [režimech připojení](connectivity.md)si můžete přečíst.

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Použití Azure Portal k vytvoření kontroleru dat ARC Azure

Pomocí následujících kroků vytvořte řadič dat ARC Azure pomocí Azure Portal a Azure Data Studio.

1. Nejdřív se přihlaste k [webu Azure Portal Marketplace](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller).  Výsledky hledání na webu Marketplace se vyfiltrují, aby se zobrazila možnost řadič dat ARC Azure.
2. Pokud první krok nezadal kritéria hledání. Zadejte prosím do výsledků hledání, klikněte na Azure ARC data Controller.
3. Vyberte dlaždici Azure Data Controller z webu Marketplace.
4. Klikněte na tlačítko **vytvořit** .
5. Přečtěte si požadavky pro vytvoření kontroleru dat ARC Azure a nainstalujte libovolný chybějící software, například Azure Data Studio a kubectl.
6. Klikněte na tlačítko **Podrobnosti řadiče dat** .
7. Vyberte předplatné, skupinu prostředků a umístění Azure stejným způsobem jako u jakýchkoli jiných prostředků, které vytvoříte v Azure Portal. V takovém případě bude umístění Azure, které vyberete, místo, kde se budou ukládat metadata o prostředku.  Samotný prostředek se vytvoří na jakékoli zvolené infrastruktuře. Nemusí být v infrastruktuře Azure.
8. Zadejte název vašeho kontroleru dat.
9. Vyberte režim připojení pro řadič dat. Přečtěte si další informace o [režimech připojení a požadavcích](./connectivity.md). 

   > [!NOTE] 
   > Pokud vyberete režim **přímého** připojení, ujistěte se, že jsou pověření instančního objektu nastavena pomocí proměnných prostředí, jak je popsáno v tématu [Vytvoření instančního objektu](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal). 

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