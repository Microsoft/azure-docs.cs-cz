---
title: Jak používat trvalé úložiště v Azure Spring Cloud | Dokumenty společnosti Microsoft
description: Jak používat trvalé úložiště v Azure Spring Cloudu
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 0e49d59386b19aa8da46b8c8e6acfe50e2124541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278538"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Použití trvalého úložiště v Azure Spring Cloudu

Azure Spring Cloud poskytuje dva typy úložiště pro vaši aplikaci: trvalé a dočasné.

Ve výchozím nastavení Poskytuje Azure Spring Cloud dočasné úložiště pro každou instanci aplikace. Dočasné úložiště je omezeno na 5 GB na instanci s výchozí cestou připojení /tmp.

> [!WARNING]
> Pokud restartujete instanci aplikace, přidružené dočasné úložiště bude trvale odstraněno.

Trvalé úložiště je kontejner sdílení souborů spravovaný Azure a přidělený pro aplikaci. Data uložená v trvalém úložišti jsou sdílena všemi instancemi aplikace. Instance Azure Spring Cloud může mít maximálně 10 aplikací s povoleným trvalým úložištěm. Každé aplikaci je přiděleno 50 GB trvalého úložiště. Výchozí cesta k připojení pro trvalé úložiště je /persistent.

> [!WARNING]
> Pokud zakážete trvalé úložiště aplikace, všechny toto úložiště je přiděleno a všechna uložená data dojde ke ztrátě.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Povolení trvalého úložiště pomocí portálu Azure

1. Na **domovské** stránce portálu Azure vyberte **Všechny prostředky**.

    >![Vyhledání ikony Všechny zdroje](media/portal-all-resources.jpg)

1. Vyberte prostředek Azure Spring Cloud, který potřebuje trvalé úložiště. V tomto příkladu se vybraná aplikace nazývá **upspring**.

    > ![Vyberte aplikaci](media/select-service.jpg)

1. V záhlaví **Nastavení** vyberte **Aplikace**.

1. Vaše služby Azure Spring Cloud se zobrazí v tabulce.  Vyberte službu, do které chcete přidat trvalé úložiště. V tomto příkladu je vybrána služba **brány.**

    > ![Vyberte si službu](media/select-gateway.jpg)

1. Na konfigurační stránce služby vyberte **Konfigurace**

1. Vyberte kartu **Trvalé úložiště** a vyberte **Povolit**.

    > ![Povolení trvalého úložiště](media/enable-persistent-storage.jpg)

Po povolení trvalého úložiště se jeho velikost a cesta zobrazí na konfigurační stránce.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Použití nastavení příkazového příkazu Azure k úpravě trvalého úložiště

V případě potřeby nainstalujte rozšíření Spring Cloud pro azure cli:

```azurecli
az extension add --name spring-cloud
```
Ostatní operace:

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
    > Pokud zakážete trvalé úložiště aplikace, všechny toto úložiště je přiděleno a všechna uložená data se trvale ztratí.

## <a name="next-steps"></a>Další kroky

* Informace o [kvótách aplikací a služeb](spring-cloud-quotas.md).
* Přečtěte si, jak [ručně škálovat aplikaci](spring-cloud-tutorial-scale-manual.md).
