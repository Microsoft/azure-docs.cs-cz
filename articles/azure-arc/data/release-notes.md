---
title: Datové služby s podporou ARC Azure – poznámky k verzi
description: Nejnovější poznámky k verzi
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3c20bbd3ab02cd1eccd00e2d36c14eebf2f63205
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92360295"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Poznámky k verzi – datové služby s podporou ARC Azure (Preview)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>Září 2020

Datové služby s podporou ARC Azure jsou vydané pro verzi Public Preview. Datové služby s povoleným obloukem umožňují spravovat datové služby kdekoli.

- Spravovaná instance SQL
- PostgreSQL škálování

Pokyny najdete v tématu [co jsou datové služby s podporou ARC Azure](overview.md) .

### <a name="known-issues"></a>Známé problémy

V této verzi se vztahují tyto problémy:

* **Odstraňuje se skupina serverů PostgreSQL se škálováním**na více instancí: Pokud jste změnili konfiguraci skupiny nebo instance serveru, počkejte, než se operace Edit dokončí, a teprve potom odstraňte skupinu serverů PostgreSQL s vlastním škálováním.

* ** `azdata notebook run` může selhat: Pokud**chcete tento problém vyřešit, spusťte `azdata notebook run` ve virtuálním prostředí Python. Tento problém se také manifestuje při neúspěšném pokusu o vytvoření spravované instance SQL nebo PostgreSQL skupiny serverů s škálovatelným škálováním pomocí Průvodce nasazením Azure Data Studio. V takovém případě můžete otevřít poznámkový blok a kliknout na tlačítko **Spustit vše** v horní části poznámkového bloku.

## <a name="next-steps"></a>Další kroky

> **Chcete něco vyzkoušet?**  
> Začněte rychle s využitím [Azure ARC rychlé zprovoznění](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) ve službě Azure Kubernetes Service (AKS), AWS elastické KUBERNETES (EKS), Google Cloud Kubernetes Engine (GKE) nebo na virtuálním počítači Azure.

[Instalace klientských nástrojů](install-client-tools.md)

[Vytvoření řadiče dat ARC Azure](create-data-controller.md) (nejprve vyžaduje instalaci nástrojů klienta)

[Vytvoření spravované instance Azure SQL v Arc Azure](create-sql-managed-instance.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)

[Vytvoření Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním na ARC Azure](create-postgresql-hyperscale-server-group.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)

## <a name="known-limitations-and-issues"></a>Známá omezení a problémy

- Názvy spravovaných instancí SQL nemohou být delší než 13 znaků.
- Žádný místní upgrade pro řadič dat nebo databázové instance ARC Azure
- Image kontejnerů datových služeb s podporou služby Arc nejsou podepsané.  Možná budete muset nakonfigurovat uzly Kubernetes tak, aby umožňovaly načítání nepodepsaných imagí kontejnerů.  Například pokud používáte Docker jako modul runtime kontejneru, můžete nastavit proměnnou prostředí DOCKER_CONTENT_TRUST = 0 a restartovat.  Ostatní moduly runtime kontejnerů, jako je [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration), obsahují podobné možnosti.
- Z Azure Portal nejde vytvořit spravované instance SQL ARC s povoleným rozšířením Azure, nebo PostgreSQL skupiny serverů.
- Pokud teď používáte systém souborů NFS, musíte před vytvořením řadiče dat ARC Azure nastavit v souboru profilu nasazení allowRunAsRoot na hodnotu true.
- Ověřování SQL a PostgreSQL přihlášení.  Žádná podpora pro Azure Active Directory ani službu Active Directory.
- Vytvoření kontroleru dat v OpenShift vyžaduje odlehčené omezení zabezpečení.  Podrobnosti najdete v dokumentaci.
- Škálování počtu Postgres _pracovních uzlů v_ rámci škálování na více systému se nepodporuje.
- Pokud používáte Azure Kubernetes Service Engine (AKS Engine) v Azure Stack hub s řadiči dat a instancemi databáze Azure ARC, upgrade na novější verzi Kubernetes není podporován. Před upgradem clusteru Kubernetes odinstalujte řadič dat ARC Azure a všechny instance databáze.
- Verze Preview nepodporuje modul zálohování a obnovení pro Postgres verze 11. Podporuje jenom zálohování a obnovení pro Postgres verze 12.
- Služba Azure Kubernetes Service (AKS), clustery, které mají [více zón dostupnosti](../../aks/availability-zones.md) , se v současné době pro datové služby s podporou ARC Azure nepodporují. Chcete-li se tomuto problému vyhnout, při vytváření clusteru AKS v Azure Portal vyberte oblast, ve které jsou zóny k dispozici, a zrušte zaškrtnutí všech zón v ovládacím prvku výběr. Viz následující obrázek:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Zrušte zaškrtnutí políček u jednotlivých zón a určete možnost žádná.":::

  
