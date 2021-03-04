---
title: Datové služby s podporou ARC Azure – známé problémy
description: Nejnovější známé problémy
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 8100d9e12f107e0c4598876c46453b46c6ee4d0e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121996"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Známé problémy – datové služby s podporou ARC Azure (Preview)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>Únor 2021

- Režim připojeného clusteru je zakázán.

## <a name="introduced-prior-to-february-2021"></a>Zavedeno před únorem 2021

### <a name="data-controller"></a>Řadič dat

- Ve službě Azure Kubernetes Service (AKS) je Kubernetes verze 1.19. x podporovaná.
- V Kubernetes 1,19 `containerd` se nepodporuje.
- Prostředek řadiče dat v Azure je aktuálně prostředek Azure. Jakékoli aktualizace, jako je třeba DELETE, se nešíří zpátky do clusteru Kubernetes.
- Názvy instancí nemůžou být delší než 13 znaků.
- Žádný místní upgrade pro řadič dat nebo databázové instance ARC Azure
- Image kontejnerů datových služeb s podporou služby Arc nejsou podepsané.  Možná budete muset nakonfigurovat uzly Kubernetes tak, aby umožňovaly načítání nepodepsaných imagí kontejnerů.  Například pokud používáte Docker jako modul runtime kontejneru, můžete nastavit proměnnou prostředí DOCKER_CONTENT_TRUST = 0 a restartovat.  Ostatní moduly runtime kontejnerů, jako je [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration), obsahují podobné možnosti.
- Z Azure Portal nejde vytvořit spravované instance SQL ARC s povoleným rozšířením Azure, nebo PostgreSQL skupiny serverů.
- Prozatím, pokud používáte systém souborů NFS, musíte `allowRunAsRoot` `true` před vytvořením řadiče dat ARC Azure nastavit v souboru profilu nasazení.
- Ověřování SQL a PostgreSQL přihlášení.  Žádná podpora pro Azure Active Directory ani službu Active Directory.
- Vytvoření kontroleru dat v OpenShift vyžaduje odlehčené omezení zabezpečení.  Podrobnosti najdete v dokumentaci.
- Pokud používáte modul Azure Kubernetes Service (AKS) v Azure Stackovém centru s řadiči dat a instancemi databáze Azure ARC, upgrade na novější verzi Kubernetes není podporován. Před upgradem clusteru Kubernetes odinstalujte řadič dat ARC Azure a všechny instance databáze.
- AKS clustery, které mají [více zón dostupnosti](../../aks/availability-zones.md) , se v současné době pro datové služby s podporou ARC Azure nepodporují. Chcete-li se tomuto problému vyhnout, při vytváření clusteru AKS v Azure Portal vyberte oblast, ve které jsou zóny k dispozici, a zrušte zaškrtnutí všech zón v ovládacím prvku výběr. Viz následující obrázek:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Zrušte zaškrtnutí políček u jednotlivých zón a určete možnost žádná.":::

### <a name="postgresql"></a>PostgreSQL

- PostgreSQL s povoleným rozšířením Azure ARC vrátí nepřesnou chybovou zprávu, pokud se nemůže obnovit k relativnímu bodu v čase, který určíte. Pokud jste například zadali bod v čase k obnovení, který je starší než vaše zálohy, obnovení se nezdaří a zobrazí se chybová zpráva, například: `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`
Pokud k tomu dojde, restartujte příkaz po určení bodu v čase, který je v rozsahu kalendářních dat, pro které máte zálohy. Tento rozsah určíte tak, že zadáte svoje zálohy a prohlížíte data, ve kterých byly provedeny.
- Obnovení bodu v čase je podporováno pouze v rámci skupin serverů. Cílový server operace obnovení bodu v čase nemůže být server, ze kterého jste provedli zálohování. Musí se jednat o jinou skupinu serverů. Úplné obnovení je však podporováno pro stejnou skupinu serverů.
- Při úplném obnovení je vyžadováno ID zálohy. Pokud neindikujete ID zálohy, použije se ve výchozím nastavení poslední záloha. Tato verze v této verzi nefunguje.

## <a name="next-steps"></a>Další kroky

> **Chcete něco vyzkoušet?**  
> Začněte rychle s využitím [Azure ARC rychlé zprovoznění](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) na AKS, AWS elastické služby KUBERNETES (EKS), modul Google Cloud Kubernetes Engine (GKE) nebo na virtuálním počítači Azure.

- [Instalace klientských nástrojů](install-client-tools.md)
- [Vytvoření řadiče dat ARC Azure](create-data-controller.md) (nejprve vyžaduje instalaci nástrojů klienta)
- [Vytvoření spravované instance Azure SQL v Arc Azure](create-sql-managed-instance.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)
- [Vytvoření Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním na ARC Azure](create-postgresql-hyperscale-server-group.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)
- [Poskytovatelé prostředků pro služby Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Poznámky k verzi – datové služby s podporou ARC Azure (Preview)](release-notes.md)
