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
ms.openlocfilehash: ee652047a33d73ece2d7648905fa590d90b1fb2f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029501"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Známé problémy – datové služby s podporou ARC Azure (Preview)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>Březen 2021

### <a name="data-controller"></a>Řadič dat

- V režimu přímého připojení můžete pomocí Azure Portal vytvořit řadič dat. Nasazení s jinými nástroji datových služeb s podporou ARC Azure se nepodporuje. Konkrétně nemůžete nasadit řadič dat v režimu přímého připojení s některým z následujících nástrojů v této verzi.
   - Azure Data Studio
   - Azure Data CLI ( `azdata` )
   - Nativní nástroje Kubernetes

   [Nasadit řadič dat ARC Azure | Režim přímého připojení](deploy-data-controller-direct-mode.md) vysvětluje, jak vytvořit řadič dat na portálu. 

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL s povoleným škálováním Azure ARC

- Nasazení skupiny serverů Postgres s povoleným rozšířením Azure ARC v řadiči dat ARC, který je povolený pro režim přímého připojení, není podporované.
- Předání neplatné hodnoty `--extensions` parametru při úpravě konfigurace skupiny serverů za účelem povolení dalších rozšíření nesprávně obnoví seznam povolených rozšíření na základě toho, co byl v době vytvoření skupiny serverů, a zabrání uživateli v vytváření dalších rozšíření. Jediným řešením dostupným v takovém případě je odstranit skupinu serverů a znovu ji nasadit.

## <a name="february-2021"></a>Únor 2021

### <a name="data-controller"></a>Řadič dat

- Režim clusteru přímého připojení je zakázaný.

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL s povoleným škálováním Azure ARC

- Obnovení bodu v čase není podporováno pro úložiště NFS v systému souborů NFS.
- Rozšíření pg_cron není možné současně povolit a konfigurovat. Pro tento postup je nutné použít dva příkazy. Jeden příkaz pro povolení a jeden příkaz pro konfiguraci. 

   Například:
   ```console
   § azdata arc postgres server edit -n myservergroup --extensions pg_cron 
   § azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
   ```

   První příkaz vyžaduje restartování skupiny serverů. Takže před provedením druhého příkazu se ujistěte, že stav skupiny serverů přešl z aktualizace na připravený. Pokud před dokončením restartování spustíte druhý příkaz, dojde k chybě. V takovém případě jednoduše počkejte na chvíli a znovu spusťte druhý příkaz.

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


## <a name="next-steps"></a>Další kroky

> **Chcete něco vyzkoušet?**  
> Začněte rychle s využitím [Azure ARC rychlé zprovoznění](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) na AKS, AWS elastické služby KUBERNETES (EKS), modul Google Cloud Kubernetes Engine (GKE) nebo na virtuálním počítači Azure.

- [Instalace klientských nástrojů](install-client-tools.md)
- [Vytvoření řadiče dat ARC Azure](create-data-controller.md) (nejprve vyžaduje instalaci nástrojů klienta)
- [Vytvoření spravované instance Azure SQL v Arc Azure](create-sql-managed-instance.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)
- [Vytvoření Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním na ARC Azure](create-postgresql-hyperscale-server-group.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)
- [Poskytovatelé prostředků pro služby Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Poznámky k verzi – datové služby s podporou ARC Azure (Preview)](release-notes.md)
