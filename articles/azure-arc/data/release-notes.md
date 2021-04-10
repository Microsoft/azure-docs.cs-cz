---
title: Datové služby s podporou ARC Azure – poznámky k verzi
description: Nejnovější poznámky k verzi
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: conceptual
ms.openlocfilehash: 2f41034331ed21e194fc2b86c2902c5957333313
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010594"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Poznámky k verzi – datové služby s podporou ARC Azure (Preview)

V tomto článku se zvýrazňují možnosti, funkce a vylepšení, které byly nedávno vydány nebo vylepšeny pro datové služby s podporou ARC Azure. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>Březen 2021

Verze z března 2021 se zavedla 6. dubna 2021.

Zkontrolujte omezení této verze ve [známých problémech – datové služby s podporou ARC Azure (Preview)](known-issues.md).

Azure Data CLI ( `azdata` ) číslo verze: 20.3.2. Můžete nainstalovat `azdata` z [instalačního rozhraní Azure Data CLI ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

### <a name="data-controller"></a>Řadič dat

- Nasaďte řadič dat datových služeb s podporou ARC Azure v režimu přímého připojení z portálu. Začněte od [nasazení data Controller – režim přímého připojení – požadavky](deploy-data-controller-direct-mode-prerequisites.md).

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL s povoleným škálováním Azure ARC

Vlastní definice prostředků (CRD) pro PostgreSQL byly konsolidovány do jediného CRD. Podívejte se na následující tabulku.

|Vydat |CRD |
|-----|-----|
|Únor 2021 a předchozí| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|Od března 2021 | postgresqls.arcdata.microsoft.com

Předchozí CRDs odstraníte při čištění minulých instalací. Viz [Vyčištění z minulých instalací](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations).

### <a name="azure-arc-enabled-managed-instance"></a>Spravovaná instance s povoleným ARC Azure

- Teď můžete obnovit databázi do spravované instance SQL se 3 replikami a automaticky se přidá do skupiny dostupnosti. 

- Nyní se můžete připojit k sekundárnímu koncovému bodu jen pro čtení na spravovaných instancích SQL nasazených se 3 replikami. Slouží `azdata arc sql endpoint list` k zobrazení sekundárního koncového bodu připojení jen pro čtení.

### <a name="known-issues"></a>Známé problémy

- V režimu přímého připojení je nahrávání využití, metrik a protokolů v tuto `azdata arc dc upload` chvíli blokované. Použití se automaticky nahraje. Nahrání pro řadič dat vytvořený v nepřímém režimu připojení by mělo fungovat i nadále.
- Nasazení řadiče dat v přímém režimu lze provádět pouze z Azure Portal a není k dispozici z klientských nástrojů, jako jsou azdata, Azure Data Studio nebo kubectl.
- Nasazení spravované instance SQL s podporou ARC Azure v přímém režimu se dá dělat jenom z Azure Portal a není dostupné z nástrojů, jako je azdata, Azure Data Studio nebo kubectl.
- Nasazení PostgeSQL s povoleným rozšířením Azure ARC v přímém režimu momentálně není k dispozici.
- Automatické odesílání dat o využití v režimu přímého připojení nebude úspěšné, pokud použijete proxy prostřednictvím `–proxy-cert <path-t-cert-file>` .

## <a name="february-2021"></a>Únor 2021

### <a name="new-capabilities-and-features"></a>Nové funkce a funkce

Azure Data CLI ( `azdata` ) číslo verze: 20.3.1. Můžete nainstalovat `azdata` z [instalačního rozhraní Azure Data CLI ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

Mezi další aktualizace patří:

- SQL Managed Instance s podporou služby Azure Arc
   - Vysoká dostupnost se skupinami dostupnosti Always On

- PostgreSQL Azure Data Studio s rozšířením Azure ARC s povoleným škálováním: 
   - Na stránce Přehled se teď zobrazuje stav vydaných skupin serverů na uzel.
   - K dispozici jsou teď nové stránky vlastností, které zobrazí další podrobnosti o skupině serverů.
   - Konfigurace parametrů stroje Postgres ze stránky **parametrů uzlu**

Problémy spojené s touto verzí najdete v tématu [známé problémy – datové služby s podporou ARC Azure (Preview)](known-issues.md) .

## <a name="january-2021"></a>Leden 2021

### <a name="new-capabilities-and-features"></a>Nové funkce a funkce

Azure Data CLI ( `azdata` ) číslo verze: 20.3.0. Můžete nainstalovat `azdata` z [instalačního rozhraní Azure Data CLI ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

Mezi další aktualizace patří:
- Lokalizovaný portál dostupný pro 17 nových jazyků
- Menší změny v Kube – nativní soubory. yaml
- Nové verze Grafana a Kibana
- Problémy s prostředími Python při použití azdata v poznámkových blocích ve Azure Data Studio vyřešené
- Rozšíření pg_audit je teď k dispozici pro PostgreSQL škálování
- Při úplném obnovení databáze PostgreSQL s škálovatelným škálováním se už nevyžadují ID zálohy.
- Stav (stav) se oznamuje pro každou instanci PostgreSQL, která představuje skupinu severů.

   V dřívějších verzích byl stav agregovaný na úrovni skupiny serverů a neseskupoval na úrovni uzlu PostgreSQL.

- PostgreSQL nasazení nyní respektují parametry velikosti svazku uvedené v příkazech Create
- Při úpravách skupiny serverů se teď uplatní parametry verze modulu.
- Zásady vytváření názvů lusků pro PostgreSQL s povoleným rozšířením Azure Arc se změnilo.

    Teď je ve formátu: `ServergroupName{c, w}-n` . Například skupina serverů se třemi uzly, jeden uzel koordinátora a dva pracovní uzly jsou reprezentovány jako:
   - `Postgres01c-0` (uzel koordinátora)
   - `Postgres01w-0` (pracovní uzel)
   - `Postgres01w-1` (pracovní uzel)

## <a name="december-2020"></a>Prosinec 2020

### <a name="new-capabilities--features"></a>Nové funkce & funkcemi

Azure Data CLI ( `azdata` ) číslo verze: 20.2.5. Můžete nainstalovat `azdata` z [instalačního rozhraní Azure Data CLI ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

Zobrazení koncových bodů pro spravovanou instanci SQL a PostgreSQL škálování pomocí příkazů Azure Data CLI ( `azdata` ) `azdata arc sql endpoint list` a `azdata arc postgres endpoint list` .

Upravte požadavky a omezení prostředku spravované instance SQL (jádra procesoru a paměti) pomocí Azure Data Studio.

PostgreSQL s povoleným rozšířením Azure teď podporuje obnovení v časovém intervalu kromě úplného zálohování pro verze 11 a 12 z PostgreSQL. Možnost obnovení k určitému bodu v čase umožňuje označit konkrétní datum a čas pro obnovení.

Zásady vytváření názvů lusků pro PostgreSQL s povoleným rozšířením Azure Arc se změnily. Teď je ve formátu: ServergroupName {r, s}-_n_. Například skupina serverů se třemi uzly, jeden uzel koordinátora a dva pracovní uzly jsou reprezentovány jako:
- `postgres02r-0` (uzel koordinátora)
- `postgres02s-0` (pracovní uzel)
- `postgres02s-1` (pracovní uzel)

### <a name="breaking-change"></a>Zásadní změna

#### <a name="new-resource-provider"></a>Nový poskytovatel prostředků

Tato verze zavádí aktualizovaného [poskytovatele prostředků](../../azure-resource-manager/management/azure-services-resource-providers.md) `Microsoft.AzureArcData` . Než budete moct tuto funkci používat, musíte zaregistrovat tohoto poskytovatele prostředků. 

Chcete-li zaregistrovat tohoto poskytovatele prostředků: 

1. V Azure Portal vyberte **předplatná** . 
2. Zvolte vaše předplatné.
3. V části **Nastavení** vyberte **poskytovatelé prostředků** . 
4. Vyhledejte `Microsoft.AzureArcData` a vyberte **Registrovat** 

Podrobné kroky najdete v těchto [typech: poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Tato změna taky odebere všechny existující prostředky Azure, které jste nahráli do Azure Portal. Pokud chcete použít poskytovatele prostředků, musíte aktualizovat řadič dat a použít nejnovější rozhraní příkazového `azdata` řádku.  

### <a name="platform-release-notes"></a>Poznámky k verzi platformy

#### <a name="direct-connectivity-mode"></a>Režim přímého připojení

Tato verze zavádí režim přímého připojení. Režim přímého připojení umožňuje řadiči dat automaticky nahrávat informace o použití do Azure. V rámci nahrávání využití se prostředek řadiče dat ARC automaticky vytvoří na portálu, pokud už není vytvořený prostřednictvím `azdata` nahrání.  

Při vytváření kontroleru dat můžete zadat přímé připojení. Následující příklad vytvoří řadič dat s `azdata arc dc create` názvem `arc` pomocí režimu přímého připojení ( `connectivity-mode direct` ). Před spuštěním tohoto příkladu nahraďte `<subscription id>` ID vašeho předplatného.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>Známé problémy

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

## <a name="october-2020"></a>Říjen 2020 

Azure Data CLI ( `azdata` ) číslo verze: 20.2.3. Můžete nainstalovat `azdata` z [instalačního rozhraní Azure Data CLI ( `azdata` )](/sql/azdata/install/deploy-install-azdata).

### <a name="breaking-changes"></a>Změny způsobující chyby

Tato verze přináší následující zásadní změny: 

* V definici vlastních prostředků PostgreSQL (CRD) se pojem `shards` přejmenuje na `workers` . Tato podmínka ( `workers` ) odpovídá názvu parametru příkazového řádku.

* `azdata arc postgres server delete` před odstraněním instance Postgres se zobrazí výzva k potvrzení.  Použijte `--force` k přeskočení výzvy.

### <a name="additional-changes"></a>Další změny

* Do volaného se přidal nový volitelný `azdata arc postgres server create` parametr `--volume-claim mounts` . Hodnota je čárkami oddělený seznam přípojných deklarací svazků. Připojení svazku je dvojice typu svazku a názvu virtuálního okruhu. Jediným typem svazku, který se aktuálně podporuje `backup` , je.  V PostgreSQL je při typu svazku `backup` připojen k okruhu PVC `/mnt/db-backups` .  To umožňuje sdílení záloh mezi instancemi PostgresSQL, aby bylo možné obnovit zálohu jedné instance PostgresSQL v jiné instanci.

* Nové krátké názvy pro vlastní definice prostředků PostgresSQL: 
  * `pg11` 
  * `pg12`
* Nahrávání telemetrie poskytuje uživateli jednu z těchto akcí:
   * Počet bodů odeslaných do Azure nebo 
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

## <a name="next-steps"></a>Další kroky

> **Chcete něco vyzkoušet?**  
> Začněte rychle s využitím [Azure ARC rychlé zprovoznění](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) na AKS, AWS elastické služby KUBERNETES (EKS), modul Google Cloud Kubernetes Engine (GKE) nebo na virtuálním počítači Azure.

- [Instalace klientských nástrojů](install-client-tools.md)
- [Vytvoření řadiče dat ARC Azure](create-data-controller.md) (nejprve vyžaduje instalaci nástrojů klienta)
- [Vytvoření spravované instance Azure SQL v Arc Azure](create-sql-managed-instance.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)
- [Vytvoření Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním na ARC Azure](create-postgresql-hyperscale-server-group.md) (vyžaduje nejprve vytvoření řadiče dat ARC Azure)
- [Poskytovatelé prostředků pro služby Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
