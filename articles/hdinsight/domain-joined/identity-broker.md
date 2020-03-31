---
title: Použití ID Broker (preview) pro správu přihlašovacích údajů- Azure HDInsight
description: Přečtěte si o službě HDInsight ID Broker, která zjednodušuje ověřování clusterů Apache Hadoop, které je připojeno k doméně.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483008"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Použití zprostředkovatele ID (preview) pro správu pověření

Tento článek popisuje, jak nastavit a používat funkci ID Broker v Azure HDInsight. Pomocí této funkce se můžete přihlásit k Apache Ambari prostřednictvím Azure Multi-Factor Authentication a získat požadované lístky kerberos bez nutnosti hash hesla ve službě Azure Active Directory Domain Services (Azure AD DS).

## <a name="overview"></a>Přehled

ID Broker zjednodušuje komplexní nastavení ověřování v následujících scénářích:

* Vaše organizace spoléhá na federaci k ověřování uživatelů pro přístup ke cloudovým prostředkům. Dříve, abyste mohli používat clustery HDInsight Enterprise Security Package (ESP), museli jste povolit synchronizaci hash hesel z místního prostředí do služby Azure Active Directory. Tento požadavek může být pro některé organizace obtížný nebo nežádoucí.

* Vytváříte řešení, která používají technologie, které jsou závislé na různých mechanismech ověřování. Například Apache Hadoop a Apache Ranger spoléhají na Kerberos, zatímco Azure Data Lake Storage spoléhá na OAuth.

ID Broker poskytuje jednotnou infrastrukturu ověřování a odstraňuje požadavek na synchronizaci hash hesel do Azure AD DS. Služba ID Broker se skládá z součástí spuštěných na virtuálním počítači windows serveru (uzel Zprostředkovatel id) spolu s uzly brány clusteru. 

Následující diagram znázorňuje tok ověřování pro všechny uživatele, včetně federovaných uživatelů, po povolení služby ID Broker:

![Tok ověřování pomocí zprostředkovatele ID](./media/identity-broker/identity-broker-architecture.png)

ID Broker umožňuje přihlásit se k clusterům ESP pomocí vícefaktorového ověřování bez zadání hesel. Pokud jste se už přihlásili k jiným službám Azure, jako je například portál Azure, můžete se přihlásit ke svému clusteru HDInsight pomocí jednotného přihlašování (SSO).

## <a name="enable-hdinsight-id-broker"></a>Povolit zprostředkovatele HDInsight ID

Chcete-li vytvořit cluster ESP s povoleným zprostředkovatelem ID, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Postupujte podle základních kroků vytvoření clusteru ESP. Další informace naleznete [v tématu Vytvoření clusteru HDInsight pomocí esp](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Vyberte **povolit zprostředkovatele HDInsight ID**.

Funkce Zprostředkovatel id přidá do clusteru jeden další virtuální virtuální ms. Tento virtuální virtuální uzel je uzel zprostředkovatele ID a obsahuje součásti serveru pro podporu ověřování. Uzel Zprostředkovatel id je doména připojená k doméně Azure AD DS.

![Možnost povolit Zprostředkovatel id](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>Integrace nástrojů

Modul plug-in HDInsight [IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) je aktualizován tak, aby podporoval OAuth. Tento modul plug-in můžete použít k připojení ke clusteru a odeslání úloh.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Přístup SSH bez hash hesla v Azure AD DS

Po povolení služby ID Broker budete stále potřebovat hash hesel uložený ve scénářích Azure AD DS pro scénáře SSH s účty domény. Chcete-li SSH do virtuálního počítače připojovaného k doméně nebo ke spuštění příkazu, `kinit` musíte zadat heslo. 

Ověřování SSH vyžaduje, aby byl hash k dispozici ve službě Azure AD DS. Pokud chcete použít SSH pouze pro scénáře správy, můžete vytvořit jeden účet pouze pro cloud a použít jej do clusteru SSH. Ostatní uživatelé mohou stále používat nástroje Ambari nebo HDInsight (například modul plug-in IntelliJ), aniž by měli k dispozici hash hesla ve službě Azure AD DS.

## <a name="next-steps"></a>Další kroky

* [Konfigurace clusteru HDInsight s balíčkem podnikového zabezpečení pomocí služby Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synchronizace uživatelů Azure Active Directory do clusteru HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorování výkonu clusteru](../hdinsight-key-scenarios-to-monitor.md)
