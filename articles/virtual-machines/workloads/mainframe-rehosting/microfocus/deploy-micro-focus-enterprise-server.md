---
title: Nasaďte Micro Enterprise Server 5,0 na AKS | Microsoft Docs
description: Přehostování úloh sálového počítače IBM z/OS pomocí vývojového a testovacího prostředí pro Azure na virtuálních počítačích Azure (virtuální počítače).
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 58ba530e434a9dc141ba8cb98120f6325eb06f7e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950469"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>Nasazení Micro Enterprise serveru 5,0 na AKS

V jiném [článku](./run-enterprise-server-container.md)jsem popsaný postupem, jak spustit Micro Enterprise Server 5,0 v kontejneru Docker. V takovém případě bych vám ukázal, jak si postupovat v jednom kroku a nasadit tuto image Docker, kterou jste vytvořili ve službě Azure Kubernetes Service (AKS).

Služba Azure Kubernetes je spravovaná služba Orchestration založená na Kubernetes. Umožňuje nasazovat, škálovat a spravovat kontejnery Docker (a další aplikace založené na kontejnerech) v rámci clusteru hostitelů kontejnerů.

Toto je proces se třemi kroky. Musíte:

1.  Vytvořte Azure Container Registry k uložení image Docker.

2.  Vytvořte cluster Azure Kubernetes pro spuštění image Docker.

3.  Spusťte aplikaci.

Díky tomu můžete horizontální navýšení kapacity (a horizontální horizontální navýšení kapacity) v Azure využít k tomu, aby tato cloudová platforma platila na maximum.

K? Pusťme se do toho.

## <a name="create-the-azure-container-registry"></a>Vytvoření Azure Container Registry

Z Azure Portal v levém horním rohu vyberte **vytvořit prostředek** . Z řídicího panelu Marketplace vyberte **kontejnery a** pak **Container Registry**. Tím přejdete do podokna **vytvořit registr kontejnerů** , ve kterém musíte vyplnit **název registru**, **předplatné Azure**, **skupinu prostředků** a **umístění**. **Název registru** musí být přeložen, takže musí být jedinečný. Vyberte **skupinu prostředků** , kterou jste použili v předchozím příspěvku blogu, a stejné odpovídající **umístění**. Pro **skladovou** položku vyberte **Povolit** pro **uživatele s oprávněními správce** a **Basic** . Jakmile máte všechno vyplněné, vyberte **vytvořit**.

![Vytvořit rozhraní registru kontejneru](media/deploy-image-1.png)

Po nasazení registru vyberte **Přejít k prostředku**. Tím přejdete k hlavnímu oknu Azure Container Registry. Dobrým prvkem je rychlé zprovoznění možnost nabídky  . Vyberte ho a zobrazí se vám pokyny k tomu, co je potřeba udělat, aby se image načetly a načetly do registru. Pojďme na to, i když:

1.  **Nainstalovat Docker** – nemusíte si dělat starosti, protože už je to hotové.

2.  **Spusťte základní image "Hello – World"** – znovu není potřeba, ale můžete to vyzkoušet.

3.  **Přihlaste se ke svému registru kontejneru** – musíte to udělat z virtuálního počítače (VM). Zkopírujte příkaz do schránky nebo poznámkového bloku.

    U vytvářeného registru příkazu je: **Docker login acrmf50.azurecr.IO**

4.  Nahrajte **do svého registru** – to je potřeba udělat pro image po rozostření i po přihlášení k virtuálnímu počítači.

5.  **Vyžádání z registru** – není relevantní pro tento návod, ale je dobré zjistit, jestli potřebujete spustit jinou image Docker.

Před opuštěním portálu musíte získat přihlašovací údaje k registru, abyste se mohli přihlásit. Ukončete okno **rychlé zprovoznění** a v nabídce registr vyberte **přístupové klíče** . Zkopírujte **uživatelské jméno** a jedno z **hesel** (Existují dva) do schránky nebo poznámkového bloku. Později je budete potřebovat k přihlášení.

Teď, když víte, co je potřeba udělat, se přihlaste k virtuálnímu počítači.

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>RDP na virtuální počítač, který jste použili k vytvoření image Docker

Vzhledem k tomu, že jste už vytvořili Docker image na serveru Windows 2016, musíte se k tomuto virtuálnímu počítači přihlásit. Z tohoto virtuálního počítače můžete vložit image do Azure Container Registry, kterou jste právě vytvořili. Přejděte na virtuální počítač v Azure Portal a pak vyberte **Přehled** a pak **připojit**. Tím se připojíte k virtuálnímu počítači přes protokol RDP (Remote Desktop Protocol). Při vytváření virtuálního počítače je potřeba použít přihlašovací údaje.

## <a name="log-in-and-push-the-image-to-the-registry"></a>Přihlášení a vložení image do registru

Po přihlášení otevřete příkazový řádek a spusťte následující příkazy Docker:

-   **Image Docker** – zobrazí seznam všech nainstalovaných IMAGÍ na virtuálním počítači. Poznamenejte si **mikrofocus/ES-acctdemo** , protože se jedná o ten, se kterým budete pracovat.

-   **acrmf50.azurecr.IO přihlášení Docker** – správný formát tady je *Docker Login \<registry name\>*. Nahraďte libovolný název, který jste použili při vytváření registru.

    -   Budete potřebovat **uživatelské jméno** a **heslo** , které jste zkopírovali z Azure Portal. Měli byste vidět aplikaci podobnou té na následujícím obrázku.

    ![Snímek obrazovky s příkazovým řádkem Správce](media/deploy-image-2.png)

-   **značka Docker acctdemo acrmf50.azurecr.IO/ES-acctdemo** – Tato značka odpovídá příslušné imagi s názvem úložiště. **Poznámka**: Pokud název \<microfocus/es-acctdemo\> nefunguje, zkuste použít úplný identifikátor image. Po provedení příkazu zadejte **Docker images – No-TRUNC –**. Měl by se zobrazit něco podobného jako u dalšího obrázku. Všimněte si, že je obrázek správně označený.

    ![Obrazovka pro výběr správce na příkazovém řádku](media/deploy-image-3.png)

-   **acrmf50.azurecr.IO/ES-acctdemo nabízených oznámení Docker** – Tato operace se aktivuje ze skutečného nabízeného oznámení do úložiště. Vzhledem k tomu, že je velikost 15 GB, spuštění může trvat několik minut. Pokud vše napadne hned, mělo by se zobrazit něco podobného jako na následujícím obrázku.

    ![Obrazovka příkazového řádku správce](media/deploy-image-4.png)

Teď se vraťte do Azure Portal, konkrétně do **úložiště**. V nabídce pro **úložiště** **Vyberte úložiště a měli** byste vidět uvedené **ES-acctdemo** . Teď vytvořte cluster AKS.

## <a name="create-the-azure-kubernetes-aks-cluster"></a>Vytvoření clusteru Azure Kubernetes (AKS)

Z Azure Portal v nabídce **Marketplace** vyberte **vytvořit prostředek** a pak **kontejnery/Kubernetes službu** . Dál je potřeba vyplnit okno **vytvořit cluster Kubernetes** . Ujistěte se, že cluster zůstane ve stejné oblasti a skupině prostředků, kterou používáte. Můžete přijmout zbytek výchozích hodnot s výjimkou **počtu uzlů,** který musí být pouze 1. Až budete hotovi, vyberte **zkontrolovat + vytvořit**.

![Obrazovka vytvoření clusteru Kubernetes](media/deploy-image-5.png)

Po dokončení nasazení umístí artefakty **služby Kubernetes** ve **skupině prostředků** , kterou jste vybrali v okně. Skutečný cluster ale bude mít vlastní skupinu prostředků vytvořenou během nasazování. Pokud v nabídce na levé straně vyberete **skupiny prostředků** , najdete je na základě konvence vytváření názvů. Tady je obrázek s minu – je to poslední z nich v seznamu.

![Snímek obrazovky se skupinami prostředků](media/deploy-image-6.png)

**Spustit bitovou kopii**

Teď je čas načíst image a spustit ji v AKS. Nejjednodušší způsob, jak to provést z Azure Portal, je použití Cloud Shell. Ikona se nachází v pravém horním rohu Azure Portal. Všimněte si, že pro tento návod používáme prostředí bash.

![Snímek obrazovky s ikonou Cloud Shell](media/deploy-image-7.png)

Po načtení prostředí zadejte tento příkaz:

**kubectl Run ES-acctdemo--image acrmf50.azurecr.io/es-acctdemo--port = 9040**

Tato operace načte image z úložiště **acrmf50.azurecr.IO** a načte ji do AKS. Pak spustí image s otevřeným portem 9040. Může se jednat o port, který jste otevřeli pro Image Docker. Potřebujete mít přístup k podnikovému serveru.

Kubernetes by měla odpovědět zprávou, že se nasazení vytvořilo.

![Snímek obrazovky se zprávou o nasazení](media/deploy-image-8.jpg)

Pokud chcete zjistit, jestli je kontejner skutečně spuštěný, zadejte: **kubectl získat lusky**.

Měli byste vidět ES-acctdemo jako běžící pod, jak je znázorněno na následujícím obrázku.

![Snímek obrazovky s acctdemo jako běžící pod](media/deploy-image-9.png)

Gratulujeme! Nyní používáte podnikový server ve službě Azure Kubernetes. V dalším článku se dozvíte, jak získat přístup ke konzole pro správu podnikového serveru a také jak využít Kubernetes k horizontálnímu navýšení kapacity nasazení.
