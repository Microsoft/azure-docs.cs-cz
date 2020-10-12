---
title: Synchronizace Azure Active Directory uživatelů s clusterem HDInsight
description: Synchronizace ověřených uživatelů z Azure Active Directory do clusteru HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 689417dd0743b01afd18b57b5336640f11edd044
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504651"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synchronizace uživatelů Azure Active Directory do clusteru HDInsight

[Clustery HDInsight s balíček zabezpečení podniku (ESP)](hdinsight-domain-joined-introduction.md) můžou používat silné ověřování s uživateli služby Azure Active Directory (Azure AD) a také používat zásady *řízení přístupu na základě rolí Azure (Azure RBAC)* . Při přidávání uživatelů a skupin do služby Azure AD můžete synchronizovat uživatele, kteří potřebují přístup ke clusteru.

## <a name="prerequisites"></a>Požadavky

Pokud jste to ještě neudělali, [vytvořte cluster HDInsight s balíček zabezpečení podniku](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Přidat nové uživatele Azure AD

Chcete-li zobrazit hostitele, otevřete webové uživatelské rozhraní Ambari. Každý uzel bude aktualizován novým nastavením bezobslužného upgradu.

1. Z [Azure Portal](https://portal.azure.com)přejděte do adresáře služby Azure AD, který je přidružený k vašemu clusteru ESP.

2. V nabídce na levé straně vyberte **Všichni uživatelé** a pak vyberte **Nový uživatel**.

    ![Azure Portal uživatelů a skupin](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Dokončete formulář nového uživatele. Vyberte skupiny, které jste vytvořili pro přiřazení oprávnění na základě clusteru. V tomto příkladu vytvořte skupinu s názvem "HiveUsers", do které můžete přiřadit nové uživatele. [Příklady pokynů](hdinsight-domain-joined-configure.md) pro vytvoření clusteru ESP zahrnují přidání dvou skupin `HiveUsers` a `AAD DC Administrators` .

    ![Azure Portal výběru skupin v podokně uživatele](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Vyberte **Vytvořit**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Použití REST API Apache Ambari k synchronizaci uživatelů

Během tohoto procesu se synchronizují skupiny uživatelů zadané při vytváření clusteru. Synchronizace uživatelů probíhá automaticky jednou za hodinu. Pokud chcete uživatele synchronizovat hned, nebo pokud chcete synchronizovat skupinu jinou než skupiny zadané během vytváření clusteru, použijte REST API Ambari.

Následující metoda používá POST s REST API Ambari. Další informace najdete v tématu [Správa clusterů HDInsight pomocí REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

1. Připojte se ke clusteru pomocí [příkazu SSH](hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte nahrazením `CLUSTERNAME` názvem vašeho clusteru a zadáním příkazu:

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

1. Tento výsledek ukazuje, že stav byl **dokončen**, byl vytvořen jeden nový uživatel a uživatel byl přiřazen členství. V tomto příkladu je uživatel přiřazený k synchronizované skupině LDAP "HiveUsers", protože uživatel se přidal do stejné skupiny ve službě Azure AD.

    > [!NOTE]  
    > Předchozí metoda synchronizuje jenom skupiny Azure AD, které jsou zadané ve vlastnosti **skupiny přístupového uživatele** v nastavení domény během vytváření clusteru. Další informace najdete v tématu  [Vytvoření clusteru HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Ověření nově přidaného uživatele Azure AD

Otevřete [webové uživatelské rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md) a ověřte, že se přidal nový uživatel Azure AD. Přístup k webovému uživatelskému rozhraní Ambari získáte tak, že přejdete na **`https://CLUSTERNAME.azurehdinsight.net`** . Zadejte uživatelské jméno a heslo správce clusteru.

1. Z řídicího panelu Ambari v nabídce **správce** vyberte **Spravovat Ambari** .

    ![Řídicí panel Apache Ambari – Správa Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Na levé straně stránky vyberte **Uživatelé** ve skupině nabídky **uživatel + Správa skupiny** .

    ![Nabídka uživatelů a skupin HDInsight](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. Nový uživatel by měl být uveden v tabulce uživatelé. Typ je nastaven na `LDAP` místo  `Local` .

    ![Přehled stránky uživatelé AAD AAD](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Přihlaste se k Ambari jako nový uživatel.

Když se nový uživatel (nebo jiný uživatel domény) přihlásí k Ambari, použije své úplné uživatelské jméno a přihlašovací údaje domény služby Azure AD.  Ambari zobrazí alias uživatele, což je zobrazované jméno uživatele ve službě Azure AD.
Nový ukázkový uživatel má uživatelské jméno `hiveuser3@contoso.com` . V Ambari se tento nový uživatel zobrazuje jako, `hiveuser3` ale uživatel se do Ambari přihlašuje jako `hiveuser3@contoso.com` .

## <a name="see-also"></a>Viz také

* [Konfigurace zásad Apache Hive ve službě HDInsight pomocí protokolu ESP](hdinsight-domain-joined-run-hive.md)
* [Správa clusterů HDInsight pomocí protokolu ESP](hdinsight-domain-joined-manage.md)
* [Autorizace uživatelů pro Apache Ambari](hdinsight-authorize-users-to-ambari.md)
