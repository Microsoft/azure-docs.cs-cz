---
title: Vytvoření PostgreSQL s povoleným rozšířením Azure ARC pomocí Azure Data Studio
description: Vytvoření PostgreSQL s povoleným rozšířením Azure ARC pomocí Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: af0cdb814433b739b15d79bec9cb399cf0a2fef7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936427"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Vytvoření PostgreSQL s povoleným rozšířením Azure ARC pomocí Azure Data Studio

Tento dokument vás provede jednotlivými kroky při použití Azure Data Studio ke zřízení skupin serverů PostgreSQL s podporou rozšíření Azure ARC.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>Připojení k řadiči dat ARC Azure

Než budete moct vytvořit instanci, přihlaste se k řadiči dat ARC Azure, pokud ještě nejste přihlášení.

```console
azdata login
```

Pak budete vyzváni k zadání oboru názvů, kde je vytvořen řadič dat, uživatelské jméno a heslo pro přihlášení k řadiči.

> Pokud potřebujete ověřit obor názvů, můžete spustit ```kubectl get pods -A``` a získat tak seznam všech oborů názvů v clusteru.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Vytvoření skupiny serverů s povoleným PostgreSQLm rozšířením Azure ARC

1. Spustit Azure Data Studio
1. Na kartě připojení klikněte na tři tečky v levém horním rohu a vyberte nové nasazení.
1. V možnostech nasazení vyberte **PostgreSQL skupina serverů s škálovatelným škálováním – Azure ARC**
    >[!NOTE]
    > Pokud není momentálně nainstalovaná, může se vám zobrazit výzva k instalaci rozhraní příkazového `azdata` řádku.
1. Přijměte ochranu osobních údajů a licenční podmínky a klikněte na tlačítko **Vybrat** v dolní části.
1. V okně nasadit PostgreSQL skupinu serverů s rozšířením Azure ARC zadejte následující informace:
   - Zadejte název skupiny serverů.
   - Zadejte a potvrďte heslo pro uživatele _Postgres_ správce skupiny serverů.
   - Vyberte třídu úložiště, která je vhodná pro data.
   - Vyberte třídu úložiště, která je vhodná pro protokoly.
   - Vyberte třídu úložiště, která je vhodná pro zálohování.
   - Vyberte počet pracovních uzlů, které se mají zřídit.
1. Klikněte na tlačítko **nasadit** .

Tím se spustí vytvoření skupiny serverů PostgreSQL s povoleným rozšířením Azure ARC na řadiči dat.

Během několika minut by se vytváření mělo úspěšně dokončit.

## <a name="next-steps"></a>Další kroky
- [Správa skupiny serverů pomocí Azure Data Studio](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [Monitorování skupiny serverů](monitor-grafana-kibana.md)
- Přečtěte si téma koncepty a návody Azure Database for PostgreSQLho škálování pro distribuci vašich dat napříč několika PostgreSQL uzly a využijte výhod všech možností škálování Azure Database for Postgres. :
    * [Uzly a tabulky](../../postgresql/concepts-hyperscale-nodes.md)
    * [Určení typu aplikace](../../postgresql/concepts-hyperscale-app-type.md)
    * [Volba distribučního sloupce](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Společné umístění tabulek](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuce a úprava tabulek](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Návrh databáze s více klienty](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Návrh řídicího panelu pro analýzu v reálném čase](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* V dokumentech výše přeskočte oddíly, **které se přihlásí k Azure Portal**, & **Vytvoření Azure Database for PostgreSQL-Citus (škálování)**. Implementujte zbývající kroky v nasazení ARC Azure. Tyto části jsou specifické pro Azure Database for PostgreSQL Citus (PaaS), které nabízíme jako službu v cloudu Azure, ale ostatní části dokumentů jsou přímo použitelné pro PostgreSQL škálování na úrovni Azure ARC.

- [Horizontální navýšení kapacity Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním](scale-out-postgresql-hyperscale-server-group.md)
- [Koncepty konfigurace úložiště a Kubernetes úložiště](storage-configuration.md)
- [Rozšiřování deklarací trvalých svazků](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Model prostředků Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

