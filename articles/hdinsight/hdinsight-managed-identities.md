---
title: Spravované identity ve službě Azure HDInsight
description: Poskytuje přehled implementace spravovaných identit ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 1081865a2e138af38ba171197719f08dedf6ffdb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408940"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Spravované identity ve službě Azure HDInsight

Spravovaná identita je identita registrovaná v Azure Active Directory (Azure AD), jejíž přihlašovací údaje jsou spravované v Azure. U spravovaných identit nemusíte registrovat instanční objekty ve službě Azure AD. Nebo Udržujte přihlašovací údaje, jako jsou certifikáty.

Spravované identity se ve službě Azure HDInsight používají pro přístup ke službám Azure AD Domain Services nebo k souborům v Azure Data Lake Storage Gen2 v případě potřeby.

Existují dva typy spravovaných identit: přiřazeno uživatelem a systémem. Azure HDInsight podporuje jenom spravované identity přiřazené uživatelem. HDInsight nepodporuje spravované identity přiřazené systémem. Spravovaná identita přiřazená uživatelem se vytvoří jako samostatný prostředek Azure, který pak můžete přiřadit k jedné nebo více instancím služby Azure. Naproti tomu spravovaná identita přiřazená systémem se vytvoří ve službě Azure AD a pak se automaticky povolí přímo na konkrétní instanci služby Azure. Životnost této spravované identity přiřazené systémem je pak svázána s životností instance služby, na které je povolena.

## <a name="hdinsight-managed-identity-implementation"></a>Implementace spravované identity HDInsight

Ve službě Azure HDInsight se spravované identity zřídí v každém uzlu clusteru. Tyto součásti identity se ale dá použít jenom ve službě HDInsight. V tuto chvíli není k dispozici žádná podporovaná metoda pro generování přístupových tokenů pomocí spravovaných identit nainstalovaných na uzlech clusteru HDInsight. U některých služeb Azure se spravované identity implementují s koncovým bodem, který můžete použít k získání přístupových tokenů. Použijte tokeny pro interakci s ostatními službami Azure sami.

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
* [Šifrování disků s využitím klíčů spravovaných zákazníky](disk-encryption.md)

## <a name="faq"></a>Nejčastější dotazy

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Co se stane, když po vytvoření clusteru odstraním spravovanou identitu?

Cluster bude v případě potřeby spravované identity nabíhat do problémů. V tuto chvíli není možné po vytvoření clusteru aktualizovat ani změnit spravovanou identitu. Proto doporučujeme, abyste se ujistili, že se spravovaná identita během modulu runtime clusteru neodstranila. Případně můžete cluster znovu vytvořit a přiřadit novou spravovanou identitu.

## <a name="next-steps"></a>Další kroky

* [Co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)
