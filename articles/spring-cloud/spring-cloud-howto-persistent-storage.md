---
title: Jak používat trvalé úložiště v Azure jaře cloudu | Microsoft Docs
description: Jak používat trvalé úložiště v Azure jaře cloudu
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: d70e7ff747b80b661e848f1c208f0d1c2c928248
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607767"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Jak používat trvalé úložiště v Azure jaře cloudu

Azure jaře Cloud nabízí pro vaši aplikaci dva typy úložiště: trvalé a dočasné.  Služba Azure jaře Cloud umožňuje ve výchozím nastavení pro každou instanci aplikace dočasné úložiště. Dočasné úložiště je omezené na 5 GB s výchozí cestou připojení: `/tmp`.

> [!WARNING]
> Po restartování instance aplikace se trvale odstraní přidružené dočasné úložiště.

Trvalé úložiště je kontejner sdílené složky spravovaný službou Azure přidělenou na aplikaci. Data uložená v trvalém úložišti se sdílejí napříč všemi instancemi aplikace. Instance služby jarní cloudová služba Azure může mít maximálně 10 aplikací s povoleným trvalým diskem. Každá aplikace přijímá 50 GB trvalého úložiště. Výchozí cesta pro připojení k trvalému úložišti je `/persistent`.

> [!WARNING]
> *Zakázáním* trvalého úložiště dojde ke zrušení přidělení úložiště pro tuto aplikaci.  Všechna data v tomto účtu úložiště se ztratí. 

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Povolení trvalého úložiště pomocí Azure Portal

1. Z domovské obrazovky Azure Portal vyberte **všechny prostředky**.

     >![Najít ikonu všechny prostředky](media/portal-all-resources.jpg)

1. Najděte a vyberte prostředek Azure pro jarní Cloud, který potřebuje trvalé úložiště.  V tomto příkladu se aplikace nazývá *jpspring*.

    > ![Vyhledání applicationb](media/select-service.jpg)

1. V záhlaví **Nastavení** vyberte **aplikace**.

1. Vaše jarní cloudové služby se zobrazí v tabulce.  Vyberte službu, do které chcete přidat trvalé úložiště.  V tomto příkladu si vybereme naši službu **brány** .

    > ![Výběr služby](media/select-gateway.jpg)

1. V okně Konfigurace služby vyberte **Konfigurace** .

1. Vyberte kartu **trvalé úložiště** a povolte trvalé úložiště.

    > ![Povolit trvalé úložiště](media/enable-persistent-storage.jpg)

Když je povolené trvalé úložiště, na této stránce se zobrazí jeho velikost a cesta.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Použití rozhraní příkazového řádku Azure pro úpravu trvalého úložiště

V případě potřeby nainstalujte pro rozhraní příkazového řádku Azure CLI rozšíření jarního cloudu:

```azurecli
az extension add --name spring-cloud
```

Vytvořte aplikaci s povoleným trvalým diskem:
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Povolení trvalého úložiště v existující aplikaci:

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
``` 

Zakázání trvalého úložiště v existující aplikaci:

> [!WARNING]
> Zakázáním trvalého úložiště dojde k přidělení úložiště pro danou aplikaci a trvale ztratíte všechna data, která byla uložena. 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>Další kroky

Přečtěte si o [kvótách aplikací a služeb](spring-cloud-quotas.md)nebo se Naučte, jak [ručně škálovat aplikaci](spring-cloud-tutorial-scale-manual.md).