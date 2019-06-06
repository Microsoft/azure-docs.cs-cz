---
title: Spravované identity v Azure HDInsight
description: Poskytuje přehled implementace spravovaných identit v Azure HDInsight.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: hrasheed
ms.openlocfilehash: 30631c4b71d1e8f3b0380a39bab49b900df32621
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427632"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Spravované identity v Azure HDInsight

Spravovaná identita je identita v Azure Active Directory (Azure AD) jehož přihlašovací údaje se spravují přes Azure. Pomocí spravované identity nemusíte registrovat instančních objektů ve službě Azure AD nebo spravovat přihlašovací údaje, jako třeba certifikáty.

Spravované identity je možné v Azure HDInsight umožňuje přístup k Azure AD domain services, přístup ke službě Azure Key Vault nebo přístup k souborům v Azure Data Lake Storage Gen2 vašich clusterů.

Existují dva typy spravovaných identit: uživatelsky přiřazené a systém přiřadil. Azure HDInsight používá uživatelem přidělenou spravovaných identit. Spravované identity přiřazené uživateli je vytvořen jako samostatný prostředek Azure, který pak můžete přiřadit jednu nebo víc instancí služby Azure. Naproti tomu systém přiřadil spravovanou identitu se vytvoří ve službě Azure AD a pak přímo na konkrétní službu Azure instanci automaticky povolí. Životnost systém přiřadil spravované identity se pak váže na životnost instance služby, který je povolen v.

## <a name="hdinsight-managed-identity-implementation"></a>Implementace HDInsight spravované identity

V Azure HDInsight jsou spravované identity zřízené na každém uzlu clusteru. Tyto komponenty identit, ale pouze použitelné ve službě HDInsight. Aktuálně neexistuje žádná podporovaná metoda pro vygenerování přístupových tokenů pomocí spravované identity nainstalovaný v uzlech clusteru HDInsight. Pro některé služby Azure jsou implementovány spravovaných identit s koncovým bodem, který můžete použít k získání přístupových tokenů pro interakci s ostatními službami Azure sami.

## <a name="create-a-managed-identity"></a>Vytvoření spravované identity

Spravované identity se dají vytvářet pomocí některé z následujících metod:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Zbývající kroky pro konfiguraci spravovanou identitu závisí na scénáři, kde se bude používat.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Spravovaná identita scénáře v Azure HDInsight

Spravované identity se používají v Azure HDInsight ve scénářích s více. Zobrazit související dokumenty pro podrobné nastavení a pokyny ke konfiguraci:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka přineste si vlastní klíč (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Další postup

* [Co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)
