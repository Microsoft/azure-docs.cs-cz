---
title: Synchronizace uživatelů služby Azure Active Directory s clusterem HDInsight
description: Synchronizujte ověřené uživatele z Azure Active Directory do clusteru HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 299d242c38152db6a471159d1f3d2803598c1832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75744858"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synchronizace uživatelů Azure Active Directory do clusteru HDInsight

[Clustery HDInsight s balíčkem zabezpečení rozlehlé sítě (ESP) můžou](hdinsight-domain-joined-introduction.md) používat silné ověřování s uživateli služby Azure Active Directory (Azure AD) a také používat zásady *řízení přístupu* (RBAC) založené na rolích. Při přidávání uživatelů a skupin do služby Azure AD můžete synchronizovat uživatele, kteří potřebují přístup k vašemu clusteru.

## <a name="prerequisites"></a>Požadavky

Pokud jste tak ještě neučinili, [vytvořte cluster HDInsight s balíčkem enterprise security .](hdinsight-domain-joined-configure.md)

## <a name="add-new-azure-ad-users"></a>Přidání nových uživatelů Azure AD

Chcete-li zobrazit hostitele, otevřete webové uživatelské uživatelské nastavení Ambari. Každý uzel bude aktualizován novým nastavením bezobslužného upgradu.

1. Z [webu Azure Portal](https://portal.azure.com)přejděte do adresáře Azure AD přidruženého k vašemu clusteru ESP.

2. V levé nabídce vyberte **Všichni uživatelé** a pak vyberte **Nový uživatel**.

    ![Uživatelé a skupiny portálu Azure](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Vyplňte nový uživatelský formulář. Vyberte skupiny, které jste vytvořili pro přiřazování oprávnění založených na clusteru. V tomto příkladu vytvořte skupinu s názvem "HiveUsers", ke které můžete přiřadit nové uživatele. [Mezi ukázkové pokyny](hdinsight-domain-joined-configure.md) pro vytvoření clusteru `HiveUsers` ESP patří přidání dvou skupin a `AAD DC Administrators`.

    ![Skupiny pro výběr uživatelského podokna portálu Azure](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Vyberte **Vytvořit**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Použití rozhraní APACHE Ambari REST API k synchronizaci uživatelů

Během tohoto procesu se synchronizují skupiny uživatelů zadané při vytváření clusteru. Synchronizace uživatelů probíhá automaticky jednou za hodinu. Chcete-li okamžitě synchronizovat uživatele nebo synchronizovat jinou skupinu než skupiny určené při vytváření clusteru, použijte rozhraní REST API Ambari.

Následující metoda používá POST s rozhraním Ambari REST API. Další informace najdete [v tématu Správa clusterů HDInsight pomocí rozhraní APACHE Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

1. Pomocí [příkazu ssh](hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený `CLUSTERNAME` příkaz nahrazením názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po ověření zadejte následující příkaz:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    Odpověď by měla vypadat takto:

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. Chcete-li zobrazit stav synchronizace, spusťte nový `curl` příkaz:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    Odpověď by měla vypadat takto:

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. Tento výsledek ukazuje, že stav je **DOKONČENO**, byl vytvořen jeden nový uživatel a uživateli bylo přiřazeno členství. V tomto příkladu je uživatel přiřazen k "HiveUsers" synchronizované ldap skupiny, protože uživatel byl přidán do stejné skupiny ve službě Azure AD.

    > [!NOTE]  
    > Předchozí metoda synchronizuje pouze skupiny Azure AD zadané ve vlastnosti **skupiny uživatelů aplikace Access** nastavení domény během vytváření clusteru. Další informace naleznete [v tématu vytvoření clusteru HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Ověření nově přidaného uživatele Azure AD

Otevřete [webové uživatelské rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md) a ověřte, že byl přidán nový uživatel Azure AD. Přístup k webovému uživatelskému uživatelskému **`https://CLUSTERNAME.azurehdinsight.net`** uživatelskému uživatelského nastavení Ambari procházením aplikace . Zadejte uživatelské jméno a heslo správce clusteru.

1. Na řídicím panelu Ambari vyberte **spravovat Ambari** v nabídce **správce.**

    ![Apache Ambari řídicí panel Správa Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Vyberte **Uživatelé** ve skupině nabídek **Uživatel + Správa skupiny** na levé straně stránky.

    ![Nabídka uživatelů a skupin hdinsightu](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. Nový uživatel by měl být uveden v tabulce Uživatelé. The Type je `LDAP` nastavena na spíše než `Local`.

    ![Přehled stránky uživatelů reklamy HDInsight](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Přihlaste se do Ambari jako nový uživatel

Když se nový uživatel (nebo jiný uživatel domény) přihlásí k Ambari, použije své úplné uživatelské jméno azure a pověření domény.  Ambari zobrazí alias uživatele, což je zobrazované jméno uživatele ve službě Azure AD.
Nový příklad uživatele má `hiveuser3@contoso.com`uživatelské jméno . V Ambari, tento nový `hiveuser3` uživatel se objeví jako ale uživatel `hiveuser3@contoso.com`přihlásí do Ambari jako .

## <a name="see-also"></a>Viz také

* [Konfigurace zásad Apache Hive v HDInsightu pomocí ESP](hdinsight-domain-joined-run-hive.md)
* [Správa clusterů HDInsight pomocí ESP](hdinsight-domain-joined-manage.md)
* [Autorizace uživatelů do Apache Ambari](hdinsight-authorize-users-to-ambari.md)
