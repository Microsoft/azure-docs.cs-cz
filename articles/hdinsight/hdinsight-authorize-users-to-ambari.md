---
title: Autorizace uživatelů pro zobrazení Ambari – Azure HDInsight
description: Jak spravovat oprávnění uživatelů a skupin Ambari pro clustery HDInsight s povoleným protokolem ESP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 3bb58334e96bf5378fb78b70125f9c7994a7c2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435648"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autorizace uživatelů pro zobrazení Apache Ambari

[Clustery HDInsight s podporou balíčků zabezpečení rozlehlé sítě (ESP)](./domain-joined/hdinsight-security-overview.md) poskytují funkce na podnikové úrovni, včetně ověřování na základě služby Azure Active Directory. Můžete [synchronizovat nové uživatele](hdinsight-sync-aad-users-to-cluster.md) přidané do skupin Azure AD, kterým byl poskytnut přístup ke clusteru, což těmto konkrétním uživatelům umožňuje provádět určité akce. Práce s uživateli, skupinami a oprávněními v [Apache Ambari](https://ambari.apache.org/) je podporována jak pro clustery ESP HDInsight, tak pro standardní clustery HDInsight.

Uživatelé služby Active Directory se mohou přihlásit k uzlům clusteru pomocí svých pověření domény. Mohou také použít svá pověření domény k ověření interakce clusteru s jinými schválenými koncovými body, jako jsou [hue](https://gethue.com/), Ambari Zobrazení, ODBC, JDBC, PowerShell a REST API.

> [!WARNING]  
> Neměňte heslo hlídacího psa Ambari (hdinsightwatchdog) ve vašem clusteru HDInsight založeném na Linuxu. Změna hesla přeruší možnost použití akcí skriptu nebo provádění operací škálování s clusterem.

Pokud jste tak ještě neučinili, postupujte [podle těchto pokynů](./domain-joined/apache-domain-joined-configure.md) a zřiďte nový cluster ESP.

## <a name="access-the-ambari-management-page"></a>Přístup na stránku správy Ambari

Chcete-li se dostat na **stránku správy Ambari** v [uživatelském uživatelském uživatelském panelu Apache Ambari ,](hdinsight-hadoop-manage-ambari.md)přejděte na stránku `https://CLUSTERNAME.azurehdinsight.net`. Zadejte uživatelské jméno a heslo správce clusteru, které jste definovali při vytváření clusteru. Dále z řídicího panelu Ambari vyberte **Spravovat Ambari** pod nabídkou **správce:**

![Správa řídicího panelu Apache Ambari](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="add-users"></a>Přidání uživatelů

### <a name="add-users-through-the-portal"></a>Přidání uživatelů prostřednictvím portálu

1. Na stránce správy vyberte **Uživatelé**.

    ![Uživatelé stránky pro správu Apache Ambari](./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png)

1. Vyberte **+ Vytvořit místního uživatele**.

1. Zadejte **uživatelské jméno** a **heslo**. Vyberte **uložit**.

### <a name="add-users-through-powershell"></a>Přidání uživatelů pomocí PowerShellu

Upravte níže uvedené proměnné `CLUSTERNAME` `NEWUSER`nahrazením `PASSWORD` , a příslušnými hodnotami.

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

### <a name="add-users-through-curl"></a>Přidání uživatelů pomocí zvlnění

Upravte níže uvedené proměnné `CLUSTERNAME` `ADMINPASSWORD`nahrazením , , `NEWUSER`a `USERPASSWORD` příslušnými hodnotami. Skript je navržen tak, aby byl proveden s bash. Pro příkazový řádek systému Windows by byly nutné drobné změny.

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

## <a name="grant-permissions-to-apache-hive-views"></a>Udělení oprávnění zobrazením Apache Hive

Ambari přichází s instancemi zobrazení apache [hive](https://hive.apache.org/) a [apache tez](https://tez.apache.org/), mimo jiné. Chcete-li udělit přístup k jedné nebo více instancí zobrazení Hive, přejděte na **stránku správy Ambari**.

1. Na stránce správy vyberte odkaz **Zobrazení** pod záhlavím nabídky **Zobrazení** vlevo.

    ![Apache Ambari zobrazení odkazů](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. Na stránce Zobrazení rozbalte řádek **HIVE.** Existuje jedno výchozí zobrazení Hive, které je vytvořeno při přidání služby Hive do clusteru. Podle potřeby můžete také vytvořit další instance zobrazení Hive. Vyberte zobrazení Hive:

    ![Zobrazení HDInsight – zobrazení Apache Hive](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Posuňte se směrem k dolnímu rohu stránky Zobrazení. V části *Oprávnění* máte dvě možnosti, jak uživatelům domény udělit oprávnění k zobrazení:

**Udělit oprávnění těmto uživatelům** ![Udělit oprávnění těmto uživatelům](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Udělit oprávnění těmto skupinám** ![Udělit oprávnění těmto skupinám](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Chcete-li přidat uživatele, vyberte tlačítko **Přidat uživatele.**

   * Začněte psát uživatelské jméno a zobrazí se rozevírací seznam dříve definovaných názvů.

     ![Apache Ambari uživatel auto dokončí](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Vyberte nebo dokončete psaní uživatelské ho. Chcete-li přidat toto uživatelské jméno jako nového uživatele, vyberte tlačítko **Nový.**

   * Chcete-li změny uložit, zaškrtněte **políčko modrá možnost**.

     ![Apache Ambari udělují uživatelská oprávnění](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Chcete-li přidat skupinu, vyberte tlačítko **Přidat skupinu.**

   * Začněte psát název skupiny. Proces výběru existujícího názvu skupiny nebo přidání nové skupiny je stejný jako při přidávání uživatelů.
   * Chcete-li změny uložit, zaškrtněte **políčko modrá možnost**.

     ![Apache Ambari udělit oprávnění](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

Přidání uživatelů přímo do zobrazení je užitečné, pokud chcete uživateli přiřadit oprávnění k použití tohoto zobrazení, ale nechcete, aby byli členy skupiny, která má další oprávnění. Chcete-li snížit množství administrativní režie, může být jednodušší přiřadit oprávnění skupinám.

## <a name="grant-permissions-to-apache-tez-views"></a>Udělení oprávnění zobrazením Apache TEZ

Instance zobrazení [Apache TEZ](https://tez.apache.org/) umožňují uživatelům sledovat a ladit všechny úlohy Tez, odeslané dotazy [Apache Hive](https://hive.apache.org/) a skripty [Apache Pig.](https://pig.apache.org/) Existuje jedna výchozí instance zobrazení Tez, která je vytvořena při zřízení clusteru.

Chcete-li přiřadit uživatele a skupiny k instanci zobrazení Tez, rozbalte řádek **TEZ** na stránce Zobrazení, jak je popsáno výše.

![Zobrazení HDInsight - zobrazení Apache Tez](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Chcete-li přidat uživatele nebo skupiny, opakujte kroky 3 až 5 v předchozí části.

## <a name="assign-users-to-roles"></a>Přiřazení uživatelů k rolím

Existuje pět rolí zabezpečení pro uživatele a skupiny, které jsou uvedeny v pořadí podle klesajících přístupových oprávnění:

* Správce clusteru
* Operátor clusteru
* Správce služeb
* Provozovatel služby
* Uživatel clusteru

Chcete-li spravovat role, přejděte na **stránku správy Ambari**a vyberte odkaz **Role** ve skupině nabídek *Clustery* vlevo.

![Apache Ambari role menu odkazy](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Chcete-li zobrazit seznam oprávnění udělených každé roli, klikněte na modrý otazník vedle záhlaví tabulky **Role** na stránce Role.

![Oprávnění k propojení nabídky rolí Apache Ambari](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Oprávnění k propojení nabídky rolí Apache Ambari")

Na této stránce jsou dvě různá zobrazení, která můžete použít ke správě rolí pro uživatele a skupiny: Blokovat a Seznam.

### <a name="block-view"></a>Blokovat zobrazení

Zobrazení Blokovat zobrazuje každou roli ve vlastním řádku a poskytuje **těmto uživatelům přiřadit role** a přiřadit role těmto **skupinám,** jak je popsáno výše.

![Apache Ambari role blokovat zobrazení](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>zobrazení seznamu

Zobrazení Seznam poskytuje možnosti rychlých úprav ve dvou kategoriích: Uživatelé a Skupiny.

* Kategorie Uživatelé v zobrazení Seznam zobrazuje seznam všech uživatelů, což umožňuje vybrat roli pro každého uživatele v rozevíracím seznamu.

    ![Zobrazení seznamu rolí Apache Ambari - uživatelé](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* Kategorie Skupiny v zobrazení Seznam zobrazuje všechny skupiny a roli přiřazenou každé skupině. V našem příkladu je seznam skupin synchronizován ze skupin Azure AD určených ve vlastnosti **skupiny uživatelů access** u nastavení domény clusteru. Viz [Vytvoření clusteru HDInsight s povoleným protokolem ESP](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).

    ![Zobrazení seznamu rolí Apache Ambari - skupiny](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Na obrázku výše je skupině "hiveusers" přiřazena role *Uživatele clusteru.* Toto je role jen pro čtení, která umožňuje uživatelům této skupiny zobrazit, ale nikoli změnit konfigurace služby a metriky clusteru.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Přihlásit se k Ambari jako uživatel pouze pro zobrazení

Jsme přiřadili naše domény Azure AD uživatele "hiveuser1" oprávnění hiveve a Tez zobrazení. Když spustíme webové uživatelské rozhraní Ambari a zadáme pověření domény tohoto uživatele (uživatelské jméno Azure AD ve formátu e-mailu a heslo), uživatel je přesměrován na stránku Zobrazení Ambari. Odtud může uživatel vybrat libovolné přístupné zobrazení. Uživatel nemůže navštívit žádnou jinou část webu, včetně řídicího panelu, služeb, hostitelů, upozornění nebo stránek správců.

![Uživatel Apache Ambari pouze se zobrazeními](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Přihlášení k Ambari jako uživatel clusteru

K roli *uživatele clusteru* jsme přiřadili našeho uživatele domény Azure AD "hiveuser2". Tato role je schopna přistupovat k řídicímu panelu a všem položkám nabídky. Uživatel clusteru má méně povolených možností než správce. Například hiveuser2 můžete zobrazit konfigurace pro každou ze služeb, ale nelze je upravit.

![Displej řídicího panelu Apache Ambari](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Další kroky

* [Konfigurace zásad Apache Hive v HDInsightu pomocí ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Správa clusterů ESP HDInsight](./domain-joined/apache-domain-joined-manage.md)
* [Použití apache hive view s Apache Hadoop v HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synchronizace uživatelů služby Azure AD s clusterem](hdinsight-sync-aad-users-to-cluster.md)
* [Správa clusterů HDInsight pomocí rozhraní Apache Ambari REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)
