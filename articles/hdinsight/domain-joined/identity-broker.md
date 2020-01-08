---
title: Použití zprostředkovatele ID (Preview) pro správu přihlašovacích údajů – Azure HDInsight
description: Přečtěte si o službě HDInsight ID Broker, která zjednodušuje ověřování pro clustery Apache Hadoop připojené k doméně.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483008"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Použití zprostředkovatele ID (Preview) pro správu přihlašovacích údajů

Tento článek popisuje, jak nastavit a použít funkci zprostředkovatele ID ve službě Azure HDInsight. Pomocí této funkce se můžete přihlásit k Apache Ambari prostřednictvím Azure Multi-Factor Authentication a získat požadované lístky protokolu Kerberos bez nutnosti použití hodnot hash hesla v Azure Active Directory Domain Services (Azure služba AD DS).

## <a name="overview"></a>Přehled

Zprostředkovatel ID zjednodušuje nastavení komplexního ověřování v následujících scénářích:

* Vaše organizace spoléhá na federaci ověřování uživatelů pro přístup k prostředkům cloudu. Předtím, aby bylo možné použít clustery HDInsight Balíček zabezpečení podniku (ESP), bylo nutné povolit synchronizaci hodnot hash hesel z místního prostředí a Azure Active Directory. Tento požadavek může být u některých organizací obtížné nebo nežádoucí.

* Vytváříte řešení, která používají technologie, které využívají různé mechanismy ověřování. Například Apache Hadoop a Apache Ranger spoléhají na protokol Kerberos, zatímco Azure Data Lake Storage spoléhá na OAuth.

Zprostředkovatel ID poskytuje jednotnou infrastrukturu ověřování a odebírá požadavek na synchronizaci hodnot hash hesel do Azure služba AD DS. Zprostředkovatel ID se skládá z komponent spuštěných na virtuálním počítači s Windows serverem (u uzlu služby ID) společně s uzly brány clusteru. 

Následující diagram znázorňuje tok ověřování pro všechny uživatele, včetně federovaných uživatelů po povolení zprostředkovatele ID:

![Tok ověřování s zprostředkovatelem ID](./media/identity-broker/identity-broker-architecture.png)

Zprostředkovatel ID umožňuje přihlásit se k clusterům ESP pomocí Multi-Factor Authentication bez zadání hesla. Pokud jste se už přihlásili k jiným službám Azure, jako je Azure Portal, můžete se ke svému clusteru HDInsight přihlásit pomocí jednotného přihlašování (SSO).

## <a name="enable-hdinsight-id-broker"></a>Povolit HDInsight ID Broker

Pokud chcete vytvořit cluster ESP s povoleným zprostředkovatelem ID, proveďte následující kroky:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Postupujte podle základních kroků vytváření pro cluster ESP. Další informace najdete v tématu [Vytvoření clusteru HDInsight s](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)protokolem ESP.
1. Vyberte **Povolit zprostředkovatele ID HDInsight**.

Funkce zprostředkovatel ID přidá do clusteru jeden další virtuální počítač. Tento virtuální počítač je uzlem služby Service Broker a zahrnuje součásti serveru pro podporu ověřování. Uzel zprostředkovatele ID je připojený k doméně Azure služba AD DS.

![Možnost povolení zprostředkovatele ID](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>Integrace nástrojů

[Modul plug-in HDInsight IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) se aktualizuje tak, aby podporoval OAuth. Tento modul plug-in můžete použít pro připojení ke clusteru a odeslání úloh.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Přístup SSH bez hodnoty hash hesla v Azure služba AD DS

Jakmile je zprostředkovatel ID povolený, budete pořád potřebovat hodnotu hash hesla uloženou v Azure služba AD DS pro scénáře SSH s doménovým účtem. Chcete-li použít SSH na virtuální počítač připojený k doméně nebo spustit příkaz `kinit`, je nutné zadat heslo. 

Ověřování SSH vyžaduje, aby byla hodnota hash k dispozici v Azure služba AD DS. Pokud chcete použít SSH jenom pro scénáře správy, můžete vytvořit jenom jeden účet jenom pro Cloud a použít ho ke clusteru SSH. Jiní uživatelé stále můžou používat Ambari nebo nástroje HDInsight (například modul plug-in IntelliJ), aniž by byl k dispozici hodnota hash hesla v Azure služba AD DS.

## <a name="next-steps"></a>Další kroky

* [Konfigurace clusteru HDInsight s Balíček zabezpečení podniku pomocí Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synchronizace Azure Active Directory uživatelů s clusterem HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorování výkonu clusteru](../hdinsight-key-scenarios-to-monitor.md)
