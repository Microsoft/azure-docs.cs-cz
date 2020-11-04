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
ms.openlocfilehash: 82dd2f16fa43b52ba4c6dfacd26da5da622523b2
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321719"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Poznámky k verzi – datové služby s podporou ARC Azure (Preview)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>Říjen 2020 

Azure Data CLI ( `azdata` ) číslo verze: 20.2.3. Stáhnout v [https://aka.ms/azdata](https://aka.ms/azdata) .

### <a name="breaking-changes"></a>Změny způsobující chyby

Tato verze přináší následující zásadní změny: 

* V definici vlastních prostředků PostgreSQL (CRD) se pojem `shards` přejmenuje na `workers` . Tato podmínka ( `workers` ) odpovídá názvu parametru příkazového řádku.

* `azdata arc postgres server delete` před odstraněním instance Postgres se zobrazí výzva k potvrzení.  Použijte `--force` k přeskočení výzvy.

### <a name="additional-changes"></a>Další změny

* Do volaného se přidal nový volitelný `azdata arc postgres server create` parametr `--volume-claim mounts` . Hodnota je čárkou oddělený seznam přípojných deklarací svazků. Připojení svazku je dvojice typu svazku a názvu virtuálního okruhu. Jediným typem svazku, který se aktuálně podporuje `backup` , je.  V PostgreSQL je při typu svazku `backup` připojen k okruhu PVC `/mnt/db-backups` .  To umožňuje sdílení záloh mezi instancemi PostgresSQL, aby bylo možné obnovit zálohu jedné instance PostgresSQL v jiné instanci.

* Nové krátké názvy pro vlastní definice prostředků PostgresSQL: 

  * `pg11` 

  * `pg12`

* Nahrávání telemetrie poskytuje uživateli jednu z těchto akcí:

   * Počet bodů odeslaných do Azure

     nebo 

   * Pokud se do Azure nezavedla žádná data, zobrazí se výzva k jejímu pokusu.

* `azdata arc dc debug copy-logs` nyní také čte ze `/var/opt/controller/log` složky a shromažďuje protokoly PostgreSQL Engine v systému Linux.

*   Zobrazit pracovní indikátor během vytváření a obnovování zálohy s PostgreSQL s měřítkem.

* `azdata arc postrgres backup list` nyní obsahuje informace o velikosti zálohy.

* Do pravého sloupce okna přehledu v Azure Portal byla přidána vlastnost název správce spravované instance SQL.

* Azure Data Studio podporuje konfiguraci počtu pracovních uzlů, vCore a nastavení paměti pro PostgreSQL s měřítkem. 

* Preview podporuje zálohování a obnovení pro Postgres verze 11 a 12.

## <a name="september-2020"></a>Září 2020

Datové služby s podporou ARC Azure jsou vydané pro verzi Public Preview. Datové služby s povoleným obloukem umožňují spravovat datové služby kdekoli.

- Spravovaná instance SQL
- PostgreSQL škálování

Pokyny najdete v tématu [co jsou datové služby s podporou ARC Azure](overview.md) .

## <a name="known-limitations-and-issues"></a>Známá omezení a problémy

- Názvy instancí nemohou být delší než 13 znaků.
- Žádný místní upgrade pro řadič dat nebo databázové instance ARC Azure
- Image kontejnerů datových služeb s podporou služby Arc nejsou podepsané.  Možná budete muset nakonfigurovat uzly Kubernetes tak, aby umožňovaly načítání nepodepsaných imagí kontejnerů.  Například pokud používáte Docker jako modul runtime kontejneru, můžete nastavit proměnnou prostředí DOCKER_CONTENT_TRUST = 0 a restartovat.  Ostatní moduly runtime kontejnerů, jako je [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration), obsahují podobné možnosti.
- Z Azure Portal nejde vytvořit spravované instance SQL ARC s povoleným rozšířením Azure, nebo PostgreSQL skupiny serverů.
- Prozatím, pokud používáte systém souborů NFS, musíte `allowRunAsRoot` `true` před vytvořením řadiče dat ARC Azure nastavit v souboru profilu nasazení.
- Ověřování SQL a PostgreSQL přihlášení.  Žádná podpora pro Azure Active Directory ani službu Active Directory.
- Vytvoření kontroleru dat v OpenShift vyžaduje odlehčené omezení zabezpečení.  Podrobnosti najdete v dokumentaci.
- Pokud používáte Azure Kubernetes Service Engine (AKS Engine) v Azure Stack hub s řadiči dat a instancemi databáze Azure ARC, upgrade na novější verzi Kubernetes není podporován. Před upgradem clusteru Kubernetes odinstalujte řadič dat ARC Azure a všechny instance databáze.
- Služba Azure Kubernetes Service (AKS), clustery, které mají [více zón dostupnosti](../../aks/availability-zones.md) , se v současné době pro datové služby s podporou ARC Azure nepodporují. Chcete-li se tomuto problému vyhnout, při vytváření clusteru AKS v Azure Portal vyberte oblast, ve které jsou zóny k dispozici, a zrušte zaškrtnutí všech zón v ovládacím prvku výběr. Viz následující obrázek:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Zrušte zaškrtnutí políček u jednotlivých zón a určete možnost žádná.":::


### <a name="known-issues-for-azure-arc-enabled-postgresql-hyperscale"></a>Známé problémy s povoleným PostgreSQLm škálováním pro Azure ARC   

- Verze Preview nepodporuje modul zálohování a obnovení pro PostgreSQL verze 11. Podporuje jenom zálohování a obnovení pro PostgreSQL verze 12.
- `azdata arc dc debug copy-logs` nnezachytí není shromažďovat protokoly PostgreSQL Engine v systému Windows.
- Opětovné vytvoření skupiny serverů s názvem skupiny serverů, kterou jste právě odstranili, může selhat nebo zablokovat. 
   - **Alternativní řešení** Nepoužívejte stejný název při opětovném vytvoření skupiny serverů nebo počkejte na Vyrovnávání zatížení/externí službu dříve odstraněné skupiny serverů. Za předpokladu, že název skupiny serverů, kterou jste odstranili `postgres01` a která byla hostována v oboru názvů `arc` , než znovu vytvoříte skupinu serverů se stejným názvem, počkejte, dokud `postgres01-external-svc` se nezobrazuje ve výstupu příkazu kubectl `kubectl get svc -n arc` .
 - Načítání stránky s přehledem a konfigurace služby COMPUTE + úložiště v Azure Data Studio je pomalé. 



## <a name="next-steps"></a>Další kroky
  
> **Chcete něco vyzkoušet?**  
> Začněte rychle s využitím [Azure ARC rychlé zprovoznění](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) ve službě Azure Kubernetes Service (AKS), AWS elastické KUBERNETES (EKS), Google Cloud Kubernetes Engine (GKE) nebo na virtuálním počítači Azure.

[Instalace klientských nástrojů](install-client-tools.md)

[Vytvoření řadiče dat ARC Azure](create-data-controller.md) (nejprve vyžaduje instalaci nástrojů klienta)

[Vytvoření spravované instance Azure SQL v Arc Azure](create-sql-managed-instance.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)

[Vytvoření Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním na ARC Azure](create-postgresql-hyperscale-server-group.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)
