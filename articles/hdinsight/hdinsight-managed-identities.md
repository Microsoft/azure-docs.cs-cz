---
title: Spravované identity ve službě Azure HDInsight
description: Poskytuje přehled implementace spravovaných identit ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: f2b7f6e8421a735db131bc05605936e8cb2d87eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944119"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Spravované identity ve službě Azure HDInsight

Spravovaná identita je identita registrovaná v Azure Active Directory (Azure AD), jejíž přihlašovací údaje jsou spravované v Azure. U spravovaných identit nemusíte registrovat instanční objekty ve službě Azure AD. Nebo Udržujte přihlašovací údaje, jako jsou certifikáty.

Spravované identity se ve službě Azure HDInsight používají pro přístup ke službám Azure AD Domain Services nebo k souborům v Azure Data Lake Storage Gen2 v případě potřeby.

Existují dva typy spravovaných identit: přiřazeno uživatelem a systémem. Azure HDInsight podporuje jenom spravované identity přiřazené uživatelem. HDInsight nepodporuje spravované identity přiřazené systémem. Spravovaná identita přiřazená uživatelem se vytvoří jako samostatný prostředek Azure, který pak můžete přiřadit k jedné nebo více instancím služby Azure. Naproti tomu spravovaná identita přiřazená systémem se vytvoří ve službě Azure AD a pak se automaticky povolí přímo na konkrétní instanci služby Azure. Životnost této spravované identity přiřazené systémem je pak svázána s životností instance služby, na které je povolena.

## <a name="hdinsight-managed-identity-implementation"></a>Implementace spravované identity HDInsight

Ve službě Azure HDInsight jsou spravované identity dostupné jenom pro interní součásti služby HDInsight. Pro přístup k externím službám není momentálně k dispozici žádná podporovaná metoda pro generování přístupových tokenů pomocí spravovaných identit nainstalovaných na uzlech clusteru HDInsight. U některých služeb Azure, jako jsou výpočetní virtuální počítače, se spravované identity implementují s koncovým bodem, který můžete použít k získání přístupových tokenů. Tento koncový bod není v uzlech HDInsight aktuálně k dispozici.

Pokud potřebujete spustit své aplikace, abyste se vyhnuli vkládání tajných klíčů a hesel do úloh analýzy (např. SCALA úlohy), můžete distribuovat vlastní certifikáty do uzlů clusteru pomocí akcí skriptů a potom použít tento certifikát k získání přístupového tokenu (například pro přístup k trezoru klíčů Azure).

## <a name="create-a-managed-identity"></a>Vytvoření spravované identity

Spravované identity lze vytvořit pomocí kterékoli z následujících metod:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Zbývající kroky konfigurace spravované identity závisí na scénáři, kde se bude používat.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scénáře spravované identity ve službě Azure HDInsight

Spravované identity se používají ve službě Azure HDInsight ve více scénářích. Podrobné pokyny k instalaci a konfiguraci najdete v souvisejících dokumentech:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#create-a-user-assigned-managed-identity)
* [Balíček zabezpečení podniku](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Šifrování disků s využitím klíčů spravovaných zákazníky](disk-encryption.md)

HDInsight bude automaticky obnovovat certifikáty pro spravované identity, které pro tyto scénáře používáte. Nicméně omezení, pokud se pro dlouhotrvající clustery používá více různých spravovaných identit, nemusí obnovení certifikátu fungovat podle očekávání pro všechny spravované identity. Pokud plánujete používat dlouho běžící clustery (například více než 60 dní), doporučujeme, abyste v důsledku tohoto omezení používali stejnou spravovanou identitu pro všechny výše uvedené scénáře. 

Pokud jste už vytvořili dlouhotrvající cluster s více různými spravovanými identitami a v některém z těchto problémů běžely tyto problémy:
 * V clusterech ESP služba Cluster Services spouští selhání nebo navýšení kapacity a jiné operace začnou selhat s chybami ověřování.
 * V clusterech ESP při změně certifikátu LDAP-DS LDAPs se certifikát LDAPs automaticky neaktualizuje, takže se nedaří spustit změny ze služby LDAP Sync a Scale.
 * Přístup MSI k neúspěšnému spuštění ADLS Gen2
 * Šifrovací klíče nelze ve scénáři CMK otáčet.

pak byste měli přiřadit požadované role a oprávnění pro výše uvedené scénáře všem spravovaným identitám používaným v clusteru. Pokud jste třeba pro clustery ADLS Gen2 a ESP používali různé spravované identity, musí mít obě tyto identity přiřazené role vlastník dat objektů BLOB úložiště a přispěvatele HDInsight Domain Services, aby se v těchto problémech nepoužívaly.

## <a name="faq"></a>Časté otázky

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Co se stane, když po vytvoření clusteru odstraním spravovanou identitu?

Cluster bude v případě potřeby spravované identity nabíhat do problémů. V tuto chvíli není možné po vytvoření clusteru aktualizovat ani změnit spravovanou identitu. Proto doporučujeme, abyste se ujistili, že se spravovaná identita během modulu runtime clusteru neodstranila. Případně můžete cluster znovu vytvořit a přiřadit novou spravovanou identitu.

## <a name="next-steps"></a>Další kroky

* [Co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)
