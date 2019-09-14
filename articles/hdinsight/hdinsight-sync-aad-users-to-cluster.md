---
title: Synchronizace Azure Active Directory uživatelů do clusteru – Azure HDInsight
description: Synchronizace ověřených uživatelů z Azure Active Directory do clusteru HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9a7008ca7967135209b8fc2c341998570a2679be
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960851"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synchronizace uživatelů Azure Active Directory do clusteru HDInsight

[Clustery HDInsight s balíček zabezpečení podniku (ESP)](hdinsight-domain-joined-introduction.md) můžou používat silné ověřování s uživateli služby Azure Active Directory (Azure AD) a také používat zásady *řízení přístupu na základě rolí* (RBAC). Při přidávání uživatelů a skupin do služby Azure AD můžete synchronizovat uživatele, kteří potřebují přístup ke clusteru.

## <a name="prerequisites"></a>Požadavky

Pokud jste to ještě neudělali, [vytvořte cluster HDInsight s balíček zabezpečení podniku](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Přidat nové uživatele Azure AD

Chcete-li zobrazit hostitele, otevřete webové uživatelské rozhraní Ambari. Každý uzel bude aktualizován novým nastavením bezobslužného upgradu.

1. V [Azure Portal](https://portal.azure.com)přejděte do adresáře služby Azure AD, který je přidružený k vašemu clusteru ESP.

2. V nabídce na levé straně vyberte **Všichni uživatelé** a pak vyberte **Nový uživatel**.

    ![Podokno všech uživatelů](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Dokončete formulář nového uživatele. Vyberte skupiny, které jste vytvořili pro přiřazení oprávnění na základě clusteru. V tomto příkladu vytvořte skupinu s názvem "HiveUsers", do které můžete přiřadit nové uživatele. [Příklady pokynů](hdinsight-domain-joined-configure.md) pro vytvoření clusteru ESP zahrnují přidání dvou skupin `HiveUsers` a. `AAD DC Administrators`

    ![Nové podokno uživatele](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Vyberte **Vytvořit**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Použití REST API Apache Ambari k synchronizaci uživatelů

Skupiny uživatelů zadané během procesu vytváření clusteru jsou v tuto chvíli synchronizovány. Synchronizace uživatelů probíhá automaticky jednou za každou hodinu. Pokud chcete uživatele synchronizovat hned, nebo pokud chcete synchronizovat skupinu jinou než skupiny zadané během vytváření clusteru, použijte REST API Ambari.

Následující metoda používá POST s REST API Ambari. Další informace najdete v tématu [Správa clusterů HDInsight pomocí REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Připojte se ke clusteru pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md). V podokně Přehled pro váš cluster v Azure Portal vyberte tlačítko **Secure Shell (SSH)** .

    ![Secure Shell (SSH)](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-secure-shell.png)

2. Zkopírujte zobrazený `ssh` příkaz a vložte ho do svého klienta ssh. Po zobrazení výzvy zadejte heslo uživatele SSH.

3. Po ověření zadejte následující příkaz:

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    Odpověď by měla vypadat takto:

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. Chcete-li zobrazit stav synchronizace, spusťte nový `curl` příkaz:

    ```bash
    curl -u admin:<YOUR PASSWORD> https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```
    
    Odpověď by měla vypadat takto:
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
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

5. Tento výsledek ukazuje, že stav byl **dokončen**, byl vytvořen jeden nový uživatel a uživatel byl přiřazen členství. V tomto příkladu je uživatel přiřazený k synchronizované skupině LDAP "HiveUsers", protože uživatel se přidal do stejné skupiny ve službě Azure AD.

> [!NOTE]  
> Předchozí metoda synchronizuje jenom skupiny Azure AD, které jsou zadané ve vlastnosti **skupiny přístupového uživatele** v nastavení domény během vytváření clusteru. Další informace najdete v tématu [Vytvoření clusteru HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Ověření nově přidaného uživatele Azure AD

Otevřete [webové uživatelské rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md) a ověřte, že se přidal nový uživatel Azure AD. Přístup k webovému uživatelskému rozhraní Ambari **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** získáte tak, že přejdete na. Zadejte uživatelské jméno a heslo správce clusteru.

1. Z řídicího panelu Ambari v nabídce **správce** vyberte **Spravovat Ambari** .

    ![Správa Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Na levé straně stránky vyberte **Uživatelé** ve skupině nabídky **uživatel + Správa skupiny** .

    ![Položka nabídky uživatelé](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. Nový uživatel by měl být uveden v tabulce uživatelé. Typ je nastaven na `LDAP` `Local`místo.

    ![Stránka Uživatelé](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Přihlaste se k Ambari jako nový uživatel.

Když se nový uživatel (nebo jiný uživatel domény) přihlásí k Ambari, použije své úplné uživatelské jméno a přihlašovací údaje domény služby Azure AD.  Ambari zobrazí alias uživatele, což je zobrazované jméno uživatele ve službě Azure AD. Nový ukázkový uživatel má uživatelské jméno `hiveuser3@contoso.com`. V Ambari se tento nový uživatel zobrazuje jako `hiveuser3` , ale uživatel se do Ambari přihlašuje jako. `hiveuser3@contoso.com`

## <a name="see-also"></a>Viz také:

* [Konfigurace zásad Apache Hive ve službě HDInsight pomocí protokolu ESP](hdinsight-domain-joined-run-hive.md)
* [Správa clusterů HDInsight pomocí protokolu ESP](hdinsight-domain-joined-manage.md)
* [Autorizace uživatelů pro Apache Ambari](hdinsight-authorize-users-to-ambari.md)
