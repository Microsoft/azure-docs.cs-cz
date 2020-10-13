---
title: Šifrování na straně serveru Azure Managed Disks – PowerShell
description: Azure Storage chrání vaše data tím, že je před tím, než je zachová v clusterech úložiště, v klidovém prostředí. Pro šifrování svých spravovaných disků můžete spoléhat na klíče spravované Microsoftem, případně můžete pomocí klíčů spravovaných zákazníkem spravovat šifrování pomocí vlastních klíčů.
author: roygara
ms.date: 09/23/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 07916b7f7e5d0f1e755ae9759a16541eede45a41
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950478"
---
# <a name="server-side-encryption-of-azure-disk-storage-for-powershell"></a>Šifrování Azure Disk Storage pro PowerShell na straně serveru

Šifrování na straně serveru (SSE) chrání vaše data a pomáhá splnit závazky zabezpečení a dodržování předpisů vaší organizace. SSE automaticky šifruje vaše data uložená na spravovaných discích Azure (s operačním systémem a datových discích) ve výchozím nastavení při uchování do cloudu. 

Data ve službě Azure Managed disks jsou transparentně šifrovaná pomocí 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), což je jedna z nejúčinnějších šifrovacích šifr, která jsou kompatibilní se standardem FIPS 140-2. Další informace o kryptografických modulech založených na službě Azure Managed disks najdete v tématu [kryptografie API: Next Generation.](/windows/desktop/seccng/cng-portal)

Šifrování na straně serveru nemá vliv na výkon spravovaných disků a neplatí žádné další náklady. 

> [!NOTE]
> Dočasné disky nejsou spravované disky a nejsou zašifrované pomocí SSE, pokud na hostiteli nepovolíte šifrování.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Pro šifrování spravovaného disku můžete spoléhat na klíče spravované platformou, nebo můžete šifrování spravovat pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, můžete zadat *klíč spravovaný zákazníkem* , který se použije k šifrování a dešifrování všech dat ve službě Managed disks. 

V následujících částech jsou podrobněji popsány všechny možnosti správy klíčů.

### <a name="platform-managed-keys"></a>Klíče spravované platformou

Ve výchozím nastavení používají spravované disky šifrovací klíče spravované platformou. Všechny spravované disky, snímky, image a data zapsaná na stávající spravované disky se automaticky zašifrují bez použití klíčů spravovaných platformou.

### <a name="customer-managed-keys"></a>Klíče spravované zákazníkem

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Omezení

Klíče spravované zákazníkem teď mají následující omezení:

- Pokud je tato funkce pro disk povolená, nemůžete ji zakázat.
    Pokud potřebujete tento problém obejít, musíte [zkopírovat všechna data](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) na zcela jiný spravovaný disk, který nepoužívá klíče spravované zákazníkem.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Podporované oblasti

Klíče spravované zákazníkem jsou k dispozici ve všech oblastech, které jsou k dispozici na spravovaných discích.

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Šifrování v rámci hostitele – komplexní šifrování pro data virtuálních počítačů

Komplexní šifrování začíná z hostitele virtuálního počítače, na kterém je server Azure, ke kterému je váš virtuální počítač přiřazený. Data na vašich dočasných discích, dočasných discích s operačním systémem a uložených mezipamětích operačního systému nebo datových disků se ukládají na tomto hostiteli virtuálních počítačů. Pokud povolíte komplexní šifrování, všechna tato data budou v klidovém stavu zašifrovaná a toky se zašifrují do služby úložiště, kde jsou trvalé. Komplexní šifrování nepoužívá procesor vašeho virtuálního počítače a nemá vliv na výkon vašeho virtuálního počítače. 

Dočasné disky a dočasné disky s operačním systémem jsou v klidovém stavu zašifrované pomocí klíčů spravovaných platformou, když povolíte komplexní šifrování. Mezipaměti operačního systému a datových disků jsou v klidovém stavu zašifrované buď pomocí klíčů spravovaných zákazníkem nebo platformou, v závislosti na typu šifrování. Pokud je například disk zašifrovaný pomocí klíčů spravovaných zákazníkem, pak je mezipaměť pro disk zašifrovaná pomocí klíčů spravovaných zákazníkem a pokud je disk zašifrovaný pomocí klíčů spravovaných platformou, bude mezipaměť pro disk zašifrovaná pomocí klíčů spravovaných platformou.

### <a name="restrictions"></a>Omezení

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Podporované oblasti

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>Dvojité šifrování v klidovém umístění

Zákazníci s vysokým zabezpečením, kteří se týkají rizik spojených s jakýmkoli konkrétním šifrovacím algoritmem, implementací nebo klíčem, se teď můžou rozhodnout pro další vrstvu šifrování pomocí jiného šifrovacího algoritmu nebo režimu v infrastruktuře, který používá šifrovací klíče spravované platformou. Tato nová vrstva se dá použít pro trvalý operační systém a datové disky, snímky a image, které se zašifrují v klidovém stavu s dvojitým šifrováním.

### <a name="supported-regions"></a>Podporované oblasti

Dvojité šifrování je k dispozici ve všech oblastech, které jsou k dispozici na spravovaných discích.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Šifrování na straně serveru oproti službě Azure Disk Encryption

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) využívá funkci [nástroje BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) systému Windows k šifrování spravovaných disků pomocí klíčů spravovaných zákazníkem v rámci virtuálního počítače hosta. Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem se zlepšuje v ADE tím, že vám umožní používat pro vaše virtuální počítače jakékoli typy operačních systémů a image šifrováním dat ve službě úložiště.

> [!IMPORTANT]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce Azure Active Directory (Azure AD). Při konfiguraci klíčů spravovaných zákazníkem se spravovaná identita automaticky přiřadí k vašim prostředkům v rámci pokrývání. Pokud později přesunete předplatné, skupinu prostředků nebo spravovaný disk z jednoho adresáře služby Azure AD do jiného, nepřesune se do nového tenanta spravovaná identita přidružená ke spravovaným diskům, takže klíče spravované zákazníkem už možná nebudou fungovat. Další informace najdete v tématu [přenos předplatného mezi adresáři služby Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).


## <a name="next-steps"></a>Další kroky

- Povolení kompletního šifrování pomocí šifrování na hostiteli s využitím [PowerShellu](disks-enable-host-based-encryption-powershell.md) nebo [Azure Portal](../disks-enable-host-based-encryption-portal.md).
- U spravovaných disků buď pomocí [PowerShellu](disks-enable-double-encryption-at-rest-powershell.md) , nebo [Azure Portal](../disks-enable-double-encryption-at-rest-portal.md)povolte dvojité šifrování v klidovém umístění.
- Povolte klíče spravované zákazníkem pro spravované disky buď pomocí [PowerShellu](disks-enable-customer-managed-keys-powershell.md) , nebo [Azure Portal](../disks-enable-customer-managed-keys-portal.md).
- [Prozkoumejte šablony Azure Resource Manager pro vytváření šifrovaných disků pomocí klíčů spravovaných zákazníkem](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Co je Azure Key Vault?](../../key-vault/general/overview.md)
