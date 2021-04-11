---
title: Jak používat trvalé úložiště v Azure jaře cloudu | Microsoft Docs
description: Jak používat trvalé úložiště v Azure jaře cloudu
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 8811a30d2d0d6a31ff722e7f470b7e7bc38c4158
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877901"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Použití trvalého úložiště v Azure Spring Cloudu

**Tento článek se týká:** ✔️ Java ✔️ C #

Azure jaře Cloud nabízí pro vaši aplikaci dva typy úložiště: trvalé a dočasné.

Ve výchozím nastavení poskytuje Azure jaře Cloud dočasné úložiště pro každou instanci aplikace. Dočasné úložiště je omezeno na 5 GB na instanci s výchozí cestou připojení/TMP.

> [!WARNING]
> Pokud restartujete instanci aplikace, přidružené dočasné úložiště se trvale odstraní.

Trvalé úložiště je kontejner sdílení souborů, který spravuje Azure a který je přidělený pro jednotlivé aplikace. Data uložená v trvalém úložišti sdílí všechny instance aplikace. Instance jarního cloudu Azure může mít maximálně 10 aplikací s povoleným trvalým úložištěm. Každé aplikaci je přiděleno 50 GB trvalého úložiště. Výchozí cesta pro připojení k trvalému úložišti je/persistent..

> [!WARNING]
> Pokud zakážete trvalé úložiště aplikací, všechna tato úložiště se oddělí a veškerá uložená data budou ztracena.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Použití Azure Portal k povolení trvalého úložiště

1. Z **domovské** stránky Azure Portal vyberte **všechny prostředky**.

    >![Najít ikonu všechny prostředky](media/portal-all-resources.jpg)

1. Vyberte prostředek Azure pro jarní Cloud, který potřebuje trvalé úložiště. V tomto příkladu se vybraná aplikace nazývá " **propružiná**".

    > ![Výběr aplikace](media/select-service.jpg)

1. V záhlaví **Nastavení** vyberte **aplikace**.

1. V tabulce se zobrazí vaše jarní cloudové služby Azure.  Vyberte službu, do které chcete přidat trvalé úložiště. V tomto příkladu je vybraná služba **brány** .

    > ![Výběr služby](media/select-gateway.jpg)

1. Na stránce konfigurace služby vyberte **Konfigurace** .

1. Vyberte kartu **trvalé úložiště** a vyberte **Povolit**.

    > ![Povolit trvalé úložiště](media/enable-persistent-storage.jpg)

Po povolení trvalého úložiště se jeho velikost a cesta zobrazí na stránce konfigurace.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Použití rozhraní příkazového řádku Azure pro úpravu trvalého úložiště

V případě potřeby nainstalujte pro rozhraní příkazového řádku Azure CLI rozšíření jarního cloudu:

```azurecli
az extension add --name spring-cloud
```
Jiné operace:

* Vytvoření aplikace s povoleným trvalým úložištěm:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Povolení trvalého úložiště pro existující aplikaci:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Zakázání trvalého úložiště v existující aplikaci:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Pokud zakážete trvalé úložiště aplikací, všechna tato úložiště se oddělí a veškerá uložená data se trvale ztratí.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [kvótách aplikací a služeb](spring-cloud-quotas.md).
* Naučte se, jak [ručně škálovat aplikaci](spring-cloud-howto-scale-manual.md).
