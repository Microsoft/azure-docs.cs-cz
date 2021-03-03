---
title: Vytvořit řadič dat v Azure Data Studio
description: Vytvořit řadič dat v Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 2b35abf7f318d4db6166dd48c4f7bd35aaf4d98a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644404"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Vytvořit řadič dat v Azure Data Studio

Můžete vytvořit řadič dat pomocí Azure Data Studio prostřednictvím Průvodce nasazením a poznámkových bloků.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Požadavky

- Potřebujete přístup ke clusteru Kubernetes a pokud chcete, aby váš soubor kubeconfig odkazoval na cluster Kubernetes, na který chcete nasadit.
- Musíte [nainstalovat nástroje klienta](install-client-tools.md) , včetně **Azure Data Studio** rozšíření Azure Data Studio s názvem **ARC Azure** a **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** .
- Musíte se přihlásit k Azure v Azure Data Studio.  Provedete to takto: zadáním CTRL/Command + SHIFT + P otevřete okno text příkazu a zadáte **Azure**.  Vyberte **Azure: přihlásit se**.   Na panelu, který se zobrazí po kliknutí na ikonu + v pravém horním rohu k přidání účtu Azure.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Použití Průvodce nasazením k vytvoření kontroleru dat ARC Azure

Pomocí těchto kroků vytvoříte řadič dat ARC Azure pomocí Průvodce nasazením.

1. V Azure Data Studio klikněte na kartu připojení na levém navigačním panelu.
2. Klikněte na tlačítko **...** v horní části panelu připojení a vyberte **nové nasazení...**
3. V Průvodci vytvořením nového nasazení zvolte možnost **řadič dat ARC Azure** a pak klikněte na tlačítko **Vybrat** v dolní části.
4. Ujistěte se, že požadované nástroje jsou k dispozici a splňují požadované verze. **Klikněte na tlačítko Další**.
5. Použijte výchozí soubor kubeconfig nebo vyberte jiný.  Klikněte na **Next** (Další).
6. Vyberte kontext clusteru Kubernetes. Klikněte na **Next** (Další).
7. Vyberte konfigurační profil nasazení v závislosti na vašem cílovém clusteru Kubernetes. **Klikněte na tlačítko Další**.
8. Pokud používáte službu Azure Red Hat OpenShift nebo službu Red Hat OpenShift Container Platform, použijte omezení kontextu zabezpečení. Postupujte podle pokynů v tématu [použití omezení kontextu zabezpečení pro datové služby s podporou ARC Azure na OpenShift](how-to-apply-security-context-constraint.md).

   >[!IMPORTANT]
   >Na platformě Azure Red Hat OpenShift nebo Red Hat OpenShift Container Platform musíte před vytvořením kontroleru dat použít omezení kontextu zabezpečení.

1. Vyberte požadované předplatné a skupinu prostředků.
1. Vyberte umístění Azure.
   
   Tady je umístění Azure, ve kterém je toto umístění v Azure, kde se uloží *metadata* o řadiči dat a instancích databáze, které spravuje. Řadič dat a instance databáze se ve skutečnosti crewted do clusteru Kubernetes, ať už to může být.

10. Vyberte příslušný režim připojení. Přečtěte si další informace o [režimech připojení](./connectivity.md). **Klikněte na tlačítko Další**.

    Pokud vyberete možnost režim přímého připojení, budou se vyžadovat přihlašovací údaje instančního objektu, jak je popsáno v tématu [Vytvoření instančního objektu](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).

11. Zadejte název pro řadič dat a pro obor názvů, ve kterém bude vytvořen řadič dat.

    Řadič dat a název oboru názvů se použijí k vytvoření vlastního prostředku v clusteru Kubernetes, aby musely odpovídat [konvencím pojmenování Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).
    
    Pokud obor názvů již existuje, bude použit, pokud obor názvů již neobsahuje jiné objekty Kubernetes – lusky atd.  Pokud obor názvů neexistuje, bude proveden pokus o vytvoření oboru názvů.  Vytvoření oboru názvů v clusteru Kubernetes vyžaduje oprávnění správce clusteru Kubernetes.  Pokud nemáte oprávnění správce clusteru Kubernetes, požádejte správce clusteru Kubernetes o provedení prvních několika kroků v článku [Vytvoření kontroleru dat pomocí Kubernetes-Native nástrojů](./create-data-controller-using-kubernetes-native-tools.md) , které musí před dokončením tohoto průvodce provést správce Kubernetes.


12. Vyberte třídu úložiště, do které se bude řadič dat nasazovat. 
13.  Zadejte uživatelské jméno a heslo a potvrďte heslo pro uživatelský účet správce kontroleru dat. Klikněte na **Next** (Další).

14. Zkontrolujte konfiguraci nasazení.
15. Kliknutím na **nasadit** nasadíte požadovanou konfiguraci nebo **skript do poznámkového bloku** , abyste si mohli prohlédnout pokyny pro nasazení nebo udělat změny, jako jsou názvy tříd úložiště nebo typy služeb. V horní části poznámkového bloku klikněte na **Spustit vše** .

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