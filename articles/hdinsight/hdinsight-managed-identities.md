---
title: Spravované identity v Azure HDInsightu
description: Poskytuje přehled implementace spravovaných identit v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: daae9c16797ad9c1b85635f5aec7d0cf884e003f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206006"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Spravované identity v Azure HDInsightu

Spravovaná identita je identita registrovaná ve službě Azure Active Directory (Azure AD), jejíž přihlašovací údaje spravuje Azure. Se spravovanými identitami nemusíte registrovat instanční objekty ve službě Azure AD ani udržovat přihlašovací údaje, jako jsou certifikáty.

Spravované identity se v Azure HDInsightu používají pro přístup ke službám domény Azure AD nebo k souborům v Azure Data Lake Storage Gen2 v případě potřeby.

Existují dva typy spravovaných identit: přiřazené uživateli a systémem. Azure HDInsight podporuje jenom spravované identity přiřazené uživatelem. HDInsight nepodporuje spravované identity přiřazené k systému. Uživatelem přiřazená spravovaná identita se vytvoří jako samostatný prostředek Azure, který pak můžete přiřadit k jedné nebo více instancí služby Azure. Naproti tomu systémem přiřazená spravovaná identita se vytvoří ve službě Azure AD a pak se automaticky aktivuje přímo v konkrétní instanci služby Azure. Životnost této spravované identity přiřazené k systému je pak vázána na životnost instance služby, na které je povolena.

## <a name="hdinsight-managed-identity-implementation"></a>Implementace spravované identity HDInsight

Ve službě Azure HDInsight se spravované identity zřaží na každém uzlu clusteru. Tyto součásti identity jsou však použitelné pouze službou HDInsight. V současné době neexistuje žádná podporovaná metoda pro generování přístupových tokenů pomocí spravovaných identit nainstalovaných v uzlech clusteru HDInsight. U některých služeb Azure se spravované identity implementují s koncovým bodem, který můžete použít k získání přístupových tokenů pro vlastní interakci s jinými službami Azure.

## <a name="create-a-managed-identity"></a>Vytvoření spravované identity

Spravované identity lze vytvořit některou z následujících metod:

* [Portál Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Zbývající kroky pro konfiguraci spravované identity závisí na scénáři, kde bude použita.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scénáře spravovaných identit v Azure HDInsightu

Spravované identity se ve službě Azure HDInsight používají ve více scénářích. Podrobné pokyny k nastavení a konfiguraci naleznete v souvisejících dokumentech:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Balíček zabezpečení podniku](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Šifrování disků s využitím klíčů spravovaných zákazníky](disk-encryption.md)

## <a name="faq"></a>Nejčastější dotazy
### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Co se stane, když po vytvoření clusteru odstraním spravovanou identitu?
Cluster se dostane do problémů, když je potřeba spravovaná identita. V současné době neexistuje žádný způsob, jak aktualizovat nebo změnit spravovat identitu po vytvoření clusteru. Takže naše doporučení je ujistěte se, že spravovaná identita není odstraněn během modulu runtime clusteru. Případně můžete znovu vytvořit cluster a přiřadit novou spravovanou identitu.

## <a name="next-steps"></a>Další kroky

* [Co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)
