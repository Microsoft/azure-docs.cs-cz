---
title: Synchronizace uživatelů Azure Active Directory do clusteru s podporou - Azure HDInsight | Microsoft Docs
description: Synchronizujte ověřené uživatele z Azure Active Directory do clusteru.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: f2deaaa31a4d0e8a91d048b538e9251a8eb9e1b7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Synchronizace uživatelů Azure Active Directory do clusteru HDInsight

[Clustery HDInsight připojený k doméně](hdinsight-domain-joined-introduction.md) může používat silné ověřování s uživateli Azure Active Directory (Azure AD), stejně jako použít *řízení přístupu na základě role* zásady (RBAC). Při přidávání uživatelů a skupin do služby Azure AD, můžete synchronizovat uživatele, kteří potřebují přístup ke clusteru.

## <a name="prerequisites"></a>Požadavky

Pokud jste zatím žádný nevytvořili, [vytvoření clusteru HDInsight se připojený k doméně](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Přidat nový Azure AD uživatelům

Chcete-li zobrazit hostitele, otevřete webové uživatelské rozhraní Ambari. Každý uzel se aktualizují pomocí nového nastavení bezobslužné instalace upgradu.

1. V [portál Azure](https://portal.azure.com), přejděte do adresáře Azure AD přidruženého k vašemu clusteru připojený k doméně.

2. Vyberte **všichni uživatelé** z nabídky na levé straně zvolte **nového uživatele**.

    ![Všichni uživatelé podokno](./media/hdinsight-sync-aad-users-to-cluster/aad-users.png)

3. Vyplňte formulář nového uživatele. Vyberte skupiny, který jste vytvořili pro přiřazení na základě clusteru oprávnění. V tomto příkladu vytvořte skupinu s názvem "HiveUsers", do které můžete přiřadit nové uživatele. [Příklad pokynů](hdinsight-domain-joined-configure.md) pro vytvoření clusteru s podporou připojený k doméně zahrnují přidání dvě skupiny, `HiveUsers` a `AAD DC Administrators`.

    ![Nové podokno uživatele](./media/hdinsight-sync-aad-users-to-cluster/aad-new-user.png)

4. Vyberte **Vytvořit**.

## <a name="use-the-ambari-rest-api-to-synchronize-users"></a>Používat k synchronizaci uživatelů Ambari REST API

Zadaný během procesu vytváření clusteru skupiny uživatelů se synchronizují v daném čase. Synchronizace uživatelských probíhá automaticky jednou za hodinu. K synchronizaci uživatelů okamžitě, nebo k synchronizaci jiné skupiny než skupiny zadané během vytváření clusteru, pomocí Ambari REST API.

Následující metodu POST používá s Ambari REST API. Další informace najdete v tématu [Správa clusterů HDInsight pomocí Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

1. [Připojit ke clusteru pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md). V podokně přehled pro váš cluster na portálu Azure, vyberte **Secure Shell (SSH)** tlačítko.

    ![Secure Shell (SSH)](./media/hdinsight-sync-aad-users-to-cluster/ssh.png)

2. Zkopírujte zobrazené `ssh` příkaz a vložte jej do vašeho klienta SSH. Zadejte ssh heslo uživatele po zobrazení výzvy.

3. Po ověření, zadejte následující příkaz:

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

4. Chcete-li zobrazit stav synchronizace, spustit novou `curl` příkaz `href` vrácena hodnota z předchozí příkaz:

    ```bash
    curl -u admin:<YOUR PASSWORD> http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1
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

5. Tento výsledek zobrazí, zda je stav **COMPLETE**, byl vytvořen jeden nový uživatel a uživateli se přiřadila členství. V tomto příkladu je uživateli přiřazeny "HiveUsers" synchronizované LDAP skupiny, protože uživatel byl přidán do tu samou skupinu ve službě Azure AD.

> [!NOTE]
> Předchozí metoda synchronizuje pouze skupiny Azure AD, zadaný v **skupiny uživatelů přístup** vlastnost nastavení domény při vytváření clusteru. Další informace najdete v tématu [vytvoření clusteru HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Ověřte nově přidané uživatele Azure AD

Otevřete [webové uživatelské rozhraní Ambari](hdinsight-hadoop-manage-ambari.md) ověřit, jestli nové uživatele Azure AD byl přidán. Přístup k webové uživatelské rozhraní Ambari procházením **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Zadejte uživatelské jméno správce clusteru a heslo.

1. Na řídicím panelu Ambari vyberte **spravovat Ambari** pod **správce** nabídky.

    ![Spravovat Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-ambari.png)

2. Vyberte **uživatelé** pod **uživatele i skupiny správy** skupiny nabídky na levé straně stránky.

    ![Uživatelé položky nabídky](./media/hdinsight-sync-aad-users-to-cluster/users-link.png)

3. Nový uživatel by měl být uvedený v tabulce uživatelů. Typ je nastavený na `LDAP` místo `Local`.

    ![Stránka uživatelé](./media/hdinsight-sync-aad-users-to-cluster/users.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Přihlaste se k Ambari jako nový uživatel

Pokud se nový uživatel (nebo jiné uživatele domény) připojí k Ambari, používají jejich úplné uživatelské jméno a doménu přihlašovací údaje Azure AD.  Ambari zobrazí alias uživatele, který je zobrazované jméno uživatele ve službě Azure AD. Nový uživatel například má uživatelské jméno `hiveuser3@contoso.com`. V Ambari, tento nový uživatel zobrazí jako `hiveuser3` ale se uživatel přihlásí do Ambari jako `hiveuser3@contoso.com`.

## <a name="see-also"></a>Další informace najdete v tématech

* [Nakonfigurovat zásady Hive v HDInsight připojený k doméně](hdinsight-domain-joined-run-hive.md)
* [Správa clusterů HDInsight připojený k doméně](hdinsight-domain-joined-manage.md)
* [Povolit uživatelům Ambari](hdinsight-authorize-users-to-ambari.md)
