---
title: Jak používat trvalé úložiště v Azure jaře cloudu | Microsoft Docs
description: Jak používat trvalé úložiště v Azure jaře cloudu
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039088"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Jak používat trvalé úložiště v Azure jaře cloudu

Azure jaře Cloud nabízí pro vaši aplikaci dva typy úložiště: trvalé a dočasné.  Služba Azure jaře Cloud umožňuje ve výchozím nastavení pro každou instanci aplikace dočasné úložiště. Dočasné úložiště je omezené na 5 GB a jeho výchozí cesta pro připojení je `/tmp`.

> [!WARNING]
> Restartování instance aplikace způsobí trvalé odstranění přidruženého dočasného úložiště.

Trvalé úložiště je kontejner sdílené složky, který spravuje Azure, který je přidělený pro každý obor aplikace. Data uložená v trvalém úložišti se sdílejí napříč všemi instancemi aplikace. Instance služby jarní cloudová služba Azure může mít maximálně 10 aplikací s povoleným trvalým diskem a každá aplikace má 50 GB trvalého úložiště. Výchozí cesta pro připojení pro trvalé úložiště je `/persistent`.

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Povolení trvalého úložiště pomocí Azure Portal

1. Na stránce vaše jarní cloudová služba Azure vyberte **řídicí panel aplikace**a pak vyberte aplikaci, která vyžaduje trvalé úložiště.
1. Na kartě **Přehled** vyhledejte atribut **trvalé úložiště** a vyberte **zakázáno**.
1. Kliknutím na **Povolit** povolte trvalé úložiště a kliknutím na tlačítko **OK** tuto změnu použijte.

Když je povolené trvalé úložiště, jeho velikost a cesta se zobrazí v atributu **trvalého úložiště** na stránce **Přehled** .

> [!WARNING]
> *Zakázáním* trvalého úložiště dojde ke zrušení přidělení úložiště pro tuto aplikaci.  Všechna data v tomto účtu úložiště se ztratí. 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>Povolení trvalého úložiště pomocí Azure CLI

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