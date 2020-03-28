---
title: 'Kurz: Konfigurace e-mailových oznámení Apache Ambari v Azure HDInsight'
description: Tento článek popisuje, jak používat SendGrid s Apache Ambari pro e-mailová oznámení.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 21376eb40fb40abe67f7e03d15aabd7d89ea62f8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082311"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Kurz: Konfigurace e-mailových oznámení Apache Ambari v Azure HDInsight

V tomto kurzu nakonfigurujete e-mailová oznámení Apache Ambari pomocí SendGrid. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) zjednodušuje správu a monitorování clusteru HDInsight tím, že poskytuje snadno použitelné webové uživatelské rozhraní a ROZHRANÍ REST API. Ambari je součástí clusterů HDInsight a používá se ke sledování clusteru a provádění změn konfigurace. [SendGrid](https://sendgrid.com/solutions/) je bezplatná cloudová e-mailová služba, která poskytuje spolehlivé transakční doručování e-mailů, škálovatelnost a analýzy v reálném čase spolu s flexibilními api, která usnadňují vlastní integraci. Zákazníci Azure můžou každý měsíc odemknout 25 000 bezplatných e-mailů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Získat uživatelské jméno Sendgrid
> * Konfigurace e-mailových oznámení Apache Ambari

## <a name="prerequisites"></a>Požadavky

* E-mailový účet SendGrid. Pokyny najdete v tématu [Jak odeslat e-mail pomocí SendGrid s Azure.](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email)

* Cluster HDInsight. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>Získat uživatelské jméno SendGrid

1. Z [portálu Azure](https://portal.azure.com)přejděte na prostředek SendGrid.

1. Na stránce Přehled vyberte **Spravovat**a přejděte na webovou stránku SendGrid pro svůj účet.

    ![Přehled SendGrid na webu Azure Portal](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. V levé nabídce přejděte na název účtu a potom **na Podrobnosti o účtu**.

    ![Navigace řídicího panelu SendGrid](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. Na stránce **Podrobnosti o účtu** zaznamenejte **uživatelské jméno**.

    ![Podrobnosti o účtu SendGrid](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Konfigurace e-mailového oznámení ambari

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`přejděte `CLUSTERNAME` na , kde je název clusteru.

1. V rozevíracím seznamu **Akce** vyberte **Spravovat oznámení**.

1. V okně **Spravovat oznámení výstrah** **+** vyberte ikonu.

    ![Ambari vytvořit upozornění](./media/apache-ambari-email/azure-portal-create-notification.png)

1. V dialogovém okně **Vytvořit upozornění** zadejte následující informace:

    |Vlastnost |Popis |
    |---|---|
    |Name (Název)|Zadejte název oznámení.|
    |Skupiny|Nakonfigurujte podle potřeby.|
    |Severity|Nakonfigurujte podle potřeby.|
    |Popis|Nepovinný parametr.|
    |Metoda|Nechte na **e-mailu**.|
    |Poslat e-mailem|Zadejte e-maily pro příjem oznámení oddělených čárkou.|
    |Server SMTP|`smtp.sendgrid.net`|
    |SMTP Port|25 nebo 587 (pro nešifrovaná/TLS připojení).|
    |E-mail od|Zadejte e-mailovou adresu. Adresa nemusí být autentická.|
    |Použití ověřování|Zaškrtněte toto políčko.|
    |Uživatelské jméno|Zadejte uživatelské jméno SendGrid.|
    |Heslo|Zadejte heslo, které jste použili při vytváření prostředku SendGrid v Azure.|
    |Potvrzení hesla|Znovu zadejte heslo.|
    |Spustit TLS|Toto políčko zaškrtněte.|

    ![Ambari vytvořit upozornění](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Vyberte **Uložit**. Vrátíte se do okna **Spravovat oznámení výstrah.**

1. V okně **Spravovat oznámení výstrah** vyberte **Zavřít**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak nakonfigurovat e-mailová oznámení Apache Ambari pomocí SendGrid. Další informace o Apache Ambari se dozvíte pomocí následujícího příkazu:

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](./hdinsight-hadoop-manage-ambari.md)

* [Vytvoření upozornění](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)
