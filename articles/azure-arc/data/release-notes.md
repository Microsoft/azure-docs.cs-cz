---
title: Datové služby s podporou ARC Azure – poznámky k verzi
description: Nejnovější poznámky k verzi
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: e7312ffd4d55f0403359f8aad2d0a8433a716f77
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280365"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Poznámky k verzi – datové služby s podporou ARC Azure (Preview)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>Říjen 2020 

### <a name="breaking-changes"></a>Změny způsobující chyby

Tato verze přináší následující zásadní změny: 

* Soubory PostgreSQL vlastní definice prostředků (CRD) nahradí pojem `shards` přejmenování na `workers` . Tato podmínka ( `workers` ) odpovídá názvu parametru příkazového řádku.

* `azdata arc postgres server delete` před odstraněním instance Postgres se zobrazí výzva k potvrzení.  Použijte `--force` k přeskočení výzvy.

### <a name="additional-changes"></a>Další změny

* Do volaného se přidal nový volitelný `azdata arc postgres server create` parametr `--volume-claim mounts` . Hodnota je čárkou oddělený seznam přípojných deklarací svazků. Připojení svazku je dvojice typu svazku a názvu virtuálního okruhu. Typ svazku nyní povoluje pouze `backup` .  V PostgreSQL je při typu svazku `backup` připojen k okruhu PVC `/mnt/db-backups` .  To umožňuje sdílení záloh mezi instancemi Postgres, aby bylo možné obnovit zálohu jedné instance Postgres v jiné.

* Nové krátké názvy pro vlastní definice prostředků PostgresSQL: 

  * `pg11` 

  * `pg12`

* Nahrávání telemetrie poskytuje uživateli jednu z těchto akcí:

   * Počet bodů odeslaných do Azure

     nebo 

   * Pokud se do Azure nezavedla žádná data, zobrazí se výzva k jejímu pokusu.

* `azdata arc dc debug copy-logs` nyní také čte ze `/var/opt/controller/log` složky a shromažďuje protokoly Postgres.

*   Zobrazit pracovní indikátor během Postgres vytváření a obnovování zálohy.

* `azdata arc postrgres backup list` nyní obsahuje informace o velikosti zálohy.

* Do pravého sloupce okna přehledu v Azure Portal byla přidána vlastnost název správce spravované instance SQL.



## <a name="september-2020"></a>Září 2020

Datové služby s podporou ARC Azure jsou vydané pro verzi Public Preview. Datové služby s povoleným obloukem umožňují spravovat datové služby kdekoli.

- Spravovaná instance SQL
- PostgreSQL škálování

Pokyny najdete v tématu [co jsou datové služby s podporou ARC Azure](overview.md) .

## <a name="known-limitations-and-issues"></a>Známá omezení a problémy

- Názvy spravovaných instancí SQL nemohou být delší než 13 znaků.
- Žádný místní upgrade pro řadič dat nebo databázové instance ARC Azure
- Image kontejnerů datových služeb s podporou služby Arc nejsou podepsané.  Možná budete muset nakonfigurovat uzly Kubernetes tak, aby umožňovaly načítání nepodepsaných imagí kontejnerů.  Například pokud používáte Docker jako modul runtime kontejneru, můžete nastavit proměnnou prostředí DOCKER_CONTENT_TRUST = 0 a restartovat.  Ostatní moduly runtime kontejnerů, jako je [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration), obsahují podobné možnosti.
- Z Azure Portal nejde vytvořit spravované instance SQL ARC s povoleným rozšířením Azure, nebo PostgreSQL skupiny serverů.
- Prozatím, pokud používáte systém souborů NFS, musíte `allowRunAsRoot` `true` před vytvořením řadiče dat ARC Azure nastavit v souboru profilu nasazení.
- Ověřování SQL a PostgreSQL přihlášení.  Žádná podpora pro Azure Active Directory ani službu Active Directory.
- Vytvoření kontroleru dat v OpenShift vyžaduje odlehčené omezení zabezpečení.  Podrobnosti najdete v dokumentaci.
- Škálování počtu PostgresSQL _pracovních uzlů v_ rámci škálování na více systému se nepodporuje.
- Pokud používáte Azure Kubernetes Service Engine (AKS Engine) v Azure Stack hub s řadiči dat a instancemi databáze Azure ARC, upgrade na novější verzi Kubernetes není podporován. Před upgradem clusteru Kubernetes odinstalujte řadič dat ARC Azure a všechny instance databáze.
- Verze Preview nepodporuje modul zálohování a obnovení pro Postgres verze 11. Podporuje jenom zálohování a obnovení pro Postgres verze 12.
- Služba Azure Kubernetes Service (AKS), clustery, které mají [více zón dostupnosti](../../aks/availability-zones.md) , se v současné době pro datové služby s podporou ARC Azure nepodporují. Chcete-li se tomuto problému vyhnout, při vytváření clusteru AKS v Azure Portal vyberte oblast, ve které jsou zóny k dispozici, a zrušte zaškrtnutí všech zón v ovládacím prvku výběr. Viz následující obrázek:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Zrušte zaškrtnutí políček u jednotlivých zón a určete možnost žádná.":::

## <a name="next-steps"></a>Další kroky
  
> **Chcete něco vyzkoušet?**  
> Začněte rychle s využitím [Azure ARC rychlé zprovoznění](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) ve službě Azure Kubernetes Service (AKS), AWS elastické KUBERNETES (EKS), Google Cloud Kubernetes Engine (GKE) nebo na virtuálním počítači Azure.

[Instalace klientských nástrojů](install-client-tools.md)

[Vytvoření řadiče dat ARC Azure](create-data-controller.md) (nejprve vyžaduje instalaci nástrojů klienta)

[Vytvoření spravované instance Azure SQL v Arc Azure](create-sql-managed-instance.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)

[Vytvoření Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním na ARC Azure](create-postgresql-hyperscale-server-group.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)
