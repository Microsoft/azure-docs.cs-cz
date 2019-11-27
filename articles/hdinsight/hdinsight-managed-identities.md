---
title: Spravované identity ve službě Azure HDInsight
description: Poskytuje přehled implementace spravovaných identit ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: e7be8fbf5f6c2c59e93d48729785dd34bae5955e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327372"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Spravované identity ve službě Azure HDInsight

Spravovaná identita je identita registrovaná v Azure Active Directory (Azure AD), jejíž přihlašovací údaje jsou spravované v Azure. U spravovaných identit nemusíte registrovat instanční objekty ve službě Azure AD ani uchovávat přihlašovací údaje, jako jsou certifikáty.

Spravované identity se dají použít ve službě Azure HDInsight, aby mohly vaše clustery přistupovat ke službám Azure AD Domain Services, přistupovat k Azure Key Vault nebo přistupovat k souborům v Azure Data Lake Storage Gen2.

Existují dva typy spravovaných identit: přiřazeno uživatelem a systémem. Azure HDInsight používá spravované identity přiřazené uživatelem. Spravovaná identita přiřazená uživatelem se vytvoří jako samostatný prostředek Azure, který pak můžete přiřadit k jedné nebo více instancím služby Azure. Naproti tomu spravovaná identita přiřazená systémem se vytvoří ve službě Azure AD a pak se automaticky povolí přímo na konkrétní instanci služby Azure. Životnost této spravované identity přiřazené systémem je pak svázána s životností instance služby, na které je povolena.

## <a name="hdinsight-managed-identity-implementation"></a>Implementace spravované identity HDInsight

Ve službě Azure HDInsight se spravované identity zřídí v každém uzlu clusteru. Tyto součásti identity se ale dá použít jenom ve službě HDInsight. V tuto chvíli není k dispozici žádná podporovaná metoda pro vygenerování přístupových tokenů pomocí spravovaných identit, které jsou nainstalované na uzlech clusteru HDInsight. U některých služeb Azure se spravované identity implementují s koncovým bodem, který můžete použít k získání přístupových tokenů pro interakci s ostatními službami Azure sami.

## <a name="create-a-managed-identity"></a>Vytvoření spravované identity

Spravované identity lze vytvořit pomocí kterékoli z následujících metod:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Zbývající kroky konfigurace spravované identity závisí na scénáři, kde se bude používat.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scénáře spravované identity ve službě Azure HDInsight

Spravované identity se používají ve službě Azure HDInsight ve více scénářích. Podrobné pokyny k instalaci a konfiguraci najdete v souvisejících dokumentech:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Balíček zabezpečení podniku](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Další kroky

* [Co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)
