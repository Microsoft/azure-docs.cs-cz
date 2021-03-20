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
ms.openlocfilehash: e2007d8f0c558d35c0507b6e12bce6d6777fad52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310899"
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

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Předběžný a dočasný krok jenom pro uživatele OpenShift

Před přechodem k dalšímu kroku implementujte tento krok. Pokud chcete nasadit skupinu serverů PostgreSQL s vlastním škálováním na Red Hat OpenShift v jiném projektu než ve výchozím nastavení, musíte pro svůj cluster spustit následující příkazy, abyste mohli aktualizovat omezení zabezpečení. Tento příkaz uděluje potřebná oprávnění účtům služeb, které budou spouštět PostgreSQL skupinu serverů s vlastním škálováním. V poli omezení kontextu zabezpečení (SCC) **_ARC – data-SCC_** je ten, který jste přidali při nasazení řadiče dat ARC Azure.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-Group-Name** je název skupiny serverů, kterou budete nasazovat během dalšího kroku._
   
Další informace o SCCs v OpenShift najdete v [dokumentaci k OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Teď můžete implementovat další krok.

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Vytvoření skupiny serverů PostgreSQL Hyperscale s podporou služby Azure Arc

1. Spustit Azure Data Studio
1. Na kartě připojení klikněte na tři tečky v levém horním rohu a vyberte nové nasazení.
1. V možnostech nasazení vyberte **PostgreSQL skupina serverů s škálovatelným škálováním – Azure ARC**
    >[!NOTE]
    > Může se zobrazit výzva k instalaci [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] sem, pokud není aktuálně nainstalovaná.
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

- [Škálování skupiny serverů Azure Database for PostgreSQL Hyperscale na více instancí](scale-out-postgresql-hyperscale-server-group.md)
- [Koncepty konfigurace úložiště a Kubernetes úložiště](storage-configuration.md)
- [Model prostředků Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

