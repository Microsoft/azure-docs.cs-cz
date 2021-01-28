---
title: 'Kurz: Konfigurace e-mailových oznámení Apache Ambari v Azure HDInsight'
description: Tento článek popisuje, jak používat SendGrid s Apache Ambari pro e-mailová oznámení.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 63d0f87925f14fddf3dbd722fa72326ee8b04f31
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946963"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Kurz: Konfigurace e-mailových oznámení Apache Ambari v Azure HDInsight

V tomto kurzu nakonfigurujete e-mailová oznámení Apache Ambari pomocí SendGrid. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) zjednodušuje správu a monitorování clusteru HDInsight tím, že poskytuje snadné použití webového uživatelského rozhraní a REST API. Ambari je součástí clusterů HDInsight a používá se k monitorování clusteru a provádění změn konfigurace. [SendGrid](https://sendgrid.com/solutions/) je bezplatná Cloudová e-mailová služba, která poskytuje spolehlivé zasílání transakčních e-mailů, škálovatelnost a analýzy v reálném čase spolu s flexibilními rozhraními API, která usnadňují vlastní integraci. Zákazníci Azure můžou každý měsíc odemknout 25 000 bezplatných e-mailů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Získat uživatelské jméno pro SendGrid
> * Konfigurace e-mailových oznámení Apache Ambari

## <a name="prerequisites"></a>Požadavky

* SendGrid e-mailový účet. Pokyny najdete v tématu [odeslání e-mailu pomocí SendGrid s Azure](../sendgrid-dotnet-how-to-send-email.md) .

* Cluster An HDInsight. Další informace najdete v tématu [Vytvoření clusterů Apache Hadoop pomocí Azure Portal](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>Získat uživatelské jméno pro SendGrid

1. Z [Azure Portal](https://portal.azure.com)přejděte na prostředek SendGrid.

1. Na stránce Přehled vyberte **Spravovat** a pro svůj účet navštivte webovou stránku SendGrid.

    ![Přehled SendGrid na webu Azure Portal](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. V nabídce vlevo přejděte na název účtu a pak na **Podrobnosti účtu**.

    ![Navigace na řídicím panelu SendGrid](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. Na stránce **Podrobnosti o účtu** si poznamenejte **uživatelské jméno**.

    ![Podrobnosti o účtu SendGrid](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Konfigurace e-mailových oznámení Ambari

1. Z webového prohlížeče přejděte do `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts` umístění, kde `CLUSTERNAME` je název vašeho clusteru.

1. V rozevíracím seznamu **Akce** vyberte **spravovat oznámení**.

1. V okně **Správa oznámení výstrah** vyberte **+** ikonu.

    ![Snímek obrazovky se zobrazí v dialogovém okně Správa oznámení výstrah.](./media/apache-ambari-email/azure-portal-create-notification.png)

1. V dialogovém okně **vytvořit oznámení výstrah** zadejte následující informace:

    |Vlastnost |Popis |
    |---|---|
    |Název|Zadejte název oznámení.|
    |Skupiny|Nakonfigurujte podle potřeby.|
    |Závažnost|Nakonfigurujte podle potřeby.|
    |Popis|Nepovinný parametr.|
    |Metoda|Ponechte v **e-mailu**.|
    |E-mail na|Zadejte e-maily pro příjem oznámení, oddělené čárkou.|
    |Server SMTP|`smtp.sendgrid.net`|
    |Port SMTP|25 nebo 587 (pro nešifrovaná připojení/TLS).|
    |E-mail z|Zadejte e-mailovou adresu. Adresa musí být platná.|
    |Použít ověřování|Zaškrtněte toto políčko.|
    |Uživatelské jméno|Zadejte uživatelské jméno SendGrid.|
    |Heslo|Zadejte heslo, které jste použili při vytváření prostředku SendGrid v Azure.|
    |Potvrzení hesla|Zadejte znovu heslo.|
    |Spustit TLS|Zaškrtněte toto políčko.|

    ![Snímek obrazovky se zobrazí v dialogovém okně vytvořit oznámení výstrah.](./media/apache-ambari-email/ambari-create-alert-notification.png)

    Vyberte **Uložit**. Vrátíte se do okna **spravovat oznámení výstrah** .

1. V okně **Správa oznámení výstrah** vyberte **Zavřít**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak nakonfigurovat e-mailová oznámení Apache Ambari pomocí SendGrid. Další informace o Apache Ambari získáte pomocí následujících kroků:

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](./hdinsight-hadoop-manage-ambari.md)

* [Vytvoření oznámení o výstrahách](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)