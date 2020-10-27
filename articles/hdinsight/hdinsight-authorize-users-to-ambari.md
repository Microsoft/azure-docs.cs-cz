---
title: Autorizace uživatelů pro zobrazení Ambari – Azure HDInsight
description: Jak spravovat oprávnění uživatelů a skupin Ambari pro clustery HDInsight s povoleným ESP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: b1cd6d93ff45d7fb40ae7ca1874343486bd0b8cb
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547924"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autorizace uživatelů pro zobrazení Apache Ambari

[Clustery HDInsight s povolenou funkcí balíček zabezpečení podniku (ESP)](./domain-joined/hdinsight-security-overview.md) poskytují funkce na podnikové úrovni, včetně ověřování pomocí Azure Active Directory. Můžete [synchronizovat nové uživatele](hdinsight-sync-aad-users-to-cluster.md) přidané do skupin Azure AD, kterým byl poskytnut přístup ke clusteru, a umožnit tak konkrétním uživatelům provádět určité akce. Práce s uživateli, skupinami a oprávněními v [Apache Ambari](https://ambari.apache.org/) se podporuje pro clustery ESP HDInsight i pro standardní clustery HDInsight.

Uživatelé služby Active Directory se můžou přihlásit k uzlům clusteru pomocí svých přihlašovacích údajů do domény. Můžou taky pomocí svých přihlašovacích údajů do domény ověřovat interakce clusteru s jinými schválenými koncovými body, jako je [odstín](https://gethue.com/), zobrazení AMBARI, ODBC, JDBC, PowerShell a rozhraní REST API.

> [!WARNING]  
> Neměňte heslo sledovacího zařízení Ambari (hdinsightwatchdog) v clusteru HDInsight se systémem Linux. Změna hesla zruší schopnost používat akce skriptu nebo provádět operace škálování s vaším clusterem.

Pokud jste to ještě neudělali, postupujte podle [těchto pokynů](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) a zřiďte nový cluster ESP.

## <a name="access-the-ambari-management-page"></a>Přístup ke stránce správy Ambari

Pokud se chcete dostat na **stránku správy Ambari** ve [webovém uživatelském rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md), přejděte na adresu `https://CLUSTERNAME.azurehdinsight.net` . Zadejte uživatelské jméno a heslo správce clusteru, které jste definovali při vytváření clusteru. Pak z řídicího panelu Ambari vyberte **Spravovat Ambari** pod nabídkou **správce** :

![Správa řídicího panelu Apache Ambari](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="add-users"></a>Přidávání uživatelů

### <a name="add-users-through-the-portal"></a>Přidávání uživatelů prostřednictvím portálu

1. Na stránce Správa vyberte možnost **Uživatelé** .

    ![Uživatelé stránky správy Apache Ambari](./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png)

1. Vyberte **+ vytvořit místního uživatele** .

1. Zadejte **uživatelské jméno** a **heslo** . Vyberte **Uložit** .

### <a name="add-users-through-powershell"></a>Přidávání uživatelů přes PowerShell

Níže uvedené proměnné upravte tak, že nahradíte `CLUSTERNAME` `NEWUSER` hodnoty, a `PASSWORD` s příslušnými hodnotami.

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>Přidat uživatele prostřednictvím oblé

Níže uvedené proměnné upravte nahrazením `CLUSTERNAME` , `ADMINPASSWORD` , a `NEWUSER` `USERPASSWORD` příslušnými hodnotami. Skript je navržený tak, aby se spustil s bash. Pro příkazový řádek systému Windows by byly potřeba drobné úpravy.

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>Udělení oprávnění Apache Hive zobrazení

Ambari obsahuje instance zobrazení pro [Apache Hive](https://hive.apache.org/) a [Apache tez](https://tez.apache.org/), mimo jiné. Chcete-li udělit přístup k jedné nebo více instancím zobrazení podregistru, přejděte na **stránku Správa Ambari** .

1. Na stránce Správa vyberte odkaz **zobrazení** v záhlaví nabídky **zobrazení** na levé straně.

    ![Odkazy na zobrazení Apache Ambari](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. Na stránce zobrazení rozbalte řádek **podregistr** . K dispozici je jedno výchozí zobrazení podregistru, které se vytvoří, když se do clusteru přidá služba podregistr. Podle potřeby můžete také vytvořit další instance zobrazení podregistru. Vyberte zobrazení podregistru:

    ![Zobrazení HDInsight – zobrazení Apache Hive](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Posuňte se k dolnímu okraji stránky zobrazení. V části *oprávnění* máte dvě možnosti, jak udělit uživatelům domény oprávnění k zobrazení:

**Udělení oprávnění těmto uživatelům** ![ Udělení oprávnění těmto uživatelům](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Udělit oprávnění těmto skupinám** ![ Udělit oprávnění těmto skupinám](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Chcete-li přidat uživatele, vyberte tlačítko **Přidat uživatele** .

   * Začněte psát uživatelské jméno a zobrazí se rozevírací seznam dříve definovaných názvů.

     ![Automatické dokončení uživatele Apache Ambari](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Vyberte nebo dokončete zadávání uživatelského jména. Chcete-li přidat toto uživatelské jméno jako nový uživatel, vyberte tlačítko **Nový** .

   * Změny uložíte tak, že vyberete **modré zaškrtávací políčko** .

     ![Apache Ambari – udělení uživatelských oprávnění](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Chcete-li přidat skupinu, vyberte tlačítko **Přidat skupinu** .

   * Začněte psát název skupiny. Proces výběru existujícího názvu skupiny nebo přidání nové skupiny je stejný jako při přidávání uživatelů.
   * Změny uložíte tak, že vyberete **modré zaškrtávací políčko** .

     ![Udělení oprávnění Apache Ambari](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

Přidávání uživatelů přímo do zobrazení je užitečné, pokud chcete uživateli přiřadit oprávnění k použití tohoto zobrazení, ale nepřejte by být členem skupiny, která má další oprávnění. Pro snížení objemu administrativních režijních nákladů může být jednodušší přiřadit oprávnění skupinám.

## <a name="grant-permissions-to-apache-tez-views"></a>Udělení oprávnění pro zobrazení Apache TEZ

Instance zobrazení [Apache tez](https://tez.apache.org/) umožňují uživatelům monitorovat a ladit všechny úlohy tez odeslané pomocí [Apache Hive](https://hive.apache.org/) dotazů a skriptů [Apache vepřového prasete](https://pig.apache.org/) . Existuje jedna výchozí instance zobrazení tez, která se vytvoří při zřízení clusteru.

Chcete-li přiřadit uživatele a skupiny k tez zobrazení instance, rozbalte řádek **tez** na stránce zobrazení, jak je popsáno výše.

![Zobrazení HDInsight – zobrazení Apache Tez](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Chcete-li přidat uživatele nebo skupiny, opakujte kroky 3-5 v předchozí části.

## <a name="assign-users-to-roles"></a>Přiřazení uživatelů k rolím

Pro uživatele a skupiny existuje pět rolí zabezpečení, které jsou uvedené v pořadí snížení přístupových oprávnění:

* Správce clusteru
* Operátor clusteru
* Správce služeb
* Operátor služby
* Uživatel clusteru

Pokud chcete spravovat role, přejděte na **stránku Správa Ambari** a pak vyberte odkaz **role** v rámci skupiny nabídky *clustery* na levé straně.

![Odkazy nabídky rolí Apache Ambari](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Pokud chcete zobrazit seznam oprávnění udělených jednotlivým rolím, klikněte na modré otazník vedle záhlaví tabulky **role** na stránce role.

![Oprávnění pro odkazy na nabídku rolí Apache Ambari](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Oprávnění pro odkazy na nabídku rolí Apache Ambari")

Na této stránce jsou k dispozici dvě různá zobrazení, která můžete použít ke správě rolí pro uživatele a skupiny: blokování a seznam.

### <a name="block-view"></a>Zobrazení blokování

Zobrazení blokování zobrazuje jednotlivé role ve vlastním řádku a poskytuje **těmto uživatelům role přiřazení** a **přiřazuje role k těmto skupinám** , jak je popsáno výše.

![Zobrazení blokování rolí Apache Ambari](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>zobrazení seznamu

Zobrazení seznamu poskytuje možnosti rychlého úprav ve dvou kategoriích: uživatelé a skupiny.

* Kategorie uživatelé v zobrazení seznamu zobrazuje seznam všech uživatelů, což vám umožní vybrat roli pro každého uživatele v rozevíracím seznamu.

    ![Zobrazení seznamu rolí Apache Ambari – uživatelé](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* V kategorii skupiny v zobrazení seznamu se zobrazí všechny skupiny a role přiřazená jednotlivým skupinám. V našem příkladu je seznam skupin synchronizovaný ze skupin Azure AD, které jsou zadané ve vlastnosti **skupiny uživatelů Access** v doméně nastavení domény. Přečtěte si téma [Vytvoření clusteru HDInsight s povoleným](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)protokolem ESP.

    ![Zobrazení seznamu rolí Apache Ambari – skupiny](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na obrázku výše je skupině "hiveusers" přiřazena role *uživatele clusteru* . Tato role je jen pro čtení, která umožňuje uživatelům této skupiny zobrazovat ale měnit konfigurace služby a metriky clusterů.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Přihlášení k Ambari jako uživatel jen pro čtení

Přiřadili jsme našemu uživateli domény Azure AD oprávnění "hiveuser1" pro zobrazení podregistru a TEZ. Když spustíme webové uživatelské rozhraní Ambari a zadáte přihlašovací údaje domény tohoto uživatele (uživatelské jméno Azure AD ve formátu e-mailu a heslo), uživatel se přesměruje na stránku zobrazení Ambari. Odsud může uživatel vybrat libovolné dostupné zobrazení. Uživatel nemůže navštívit žádnou jinou část webu, včetně řídicího panelu, služeb, hostitelů, výstrah nebo stránek správce.

![Uživatel Apache Ambari jenom s zobrazeními](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Přihlášení k Ambari jako uživatel clusteru

K roli *uživatele clusteru* jsme přiřadili uživatele domény služby Azure AD "hiveuser2". Tato role může mít přístup k řídicímu panelu a všem položkám nabídky. Uživatel clusteru má méně povolených možností než správce. Hiveuser2 může například zobrazit konfigurace pro každou ze služeb, ale nemůže je upravovat.

![Zobrazení řídicího panelu Apache Ambari](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Další kroky

* [Konfigurace zásad Apache Hive ve službě HDInsight pomocí protokolu ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Správa clusterů s protokolem ESP HDInsight](./domain-joined/apache-domain-joined-manage.md)
* [Použití zobrazení Apache Hive s Apache Hadoop ve službě HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synchronizace uživatelů Azure AD s clusterem](hdinsight-sync-aad-users-to-cluster.md)
* [Správa clusterů HDInsight pomocí REST API Apache Ambari](./hdinsight-hadoop-manage-ambari-rest-api.md)