---
title: Šifrování služby Azure Storage pro neaktivní uložená data
description: Azure Storage chrání vaše data tím, že je před trvalým nasazením do cloudu automaticky šifruje. Pro šifrování dat ve vašem účtu úložiště můžete spoléhat na klíče spravované Microsoftem, nebo můžete šifrování spravovat pomocí vlastních klíčů.
services: storage
author: tamram
ms.service: storage
ms.date: 08/24/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 1a5ed324ae109a151bf21050993bff02434410df
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814446"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Šifrování služby Azure Storage pro neaktivní uložená data

Azure Storage automaticky šifruje vaše data, když se trvale uloží do cloudu. Azure Storage šifrování chrání vaše data a umožňuje vám plnit závazky zabezpečení vaší organizace a dodržování předpisů.

## <a name="about-azure-storage-encryption"></a>O šifrování Azure Storage

Data v Azure Storage jsou šifrována a dešifrována transparentně pomocí 256 [šifrování AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), je k dispozici jedna z nejúčinnějších šifrovacích šifr a je kompatibilní se standardem FIPS 140-2. Šifrování Azure Storage se v systému Windows podobá šifrování BitLockeru.

Azure Storage šifrování je povolené pro všechny účty úložiště, včetně účtů úložiště Správce prostředků a Classic. Šifrování Azure Storage nelze zakázat. Vzhledem k tomu, že vaše data jsou zabezpečená ve výchozím nastavení, nemusíte upravovat kód ani aplikace, abyste mohli využívat Azure Storage šifrování.

Data v účtu úložiště jsou šifrovaná bez ohledu na úroveň výkonu (Standard nebo Premium), úroveň přístupu (horkou nebo studenou) nebo model nasazení (Azure Resource Manager nebo klasický). Všechny objekty BLOB v archivní úrovni jsou také šifrované. Všechny možnosti redundance Azure Storage podporují šifrování a všechna data v primární i sekundární oblasti jsou zašifrovaná, pokud je geografická replikace povolená. Všechny prostředky Azure Storage jsou zašifrované, včetně objektů blob, disků, souborů, front a tabulek. Všechna metadata objektů jsou také šifrována. Azure Storage šifrování se neúčtují žádné další náklady.

Všechny objekty blob bloku, doplňovací objekty blob nebo objekty blob stránky, které byly zapsány do Azure Storage po 20. října 2017, jsou zašifrovány. Objekty blob vytvořené před tímto datem budou i nadále zašifrovány procesem na pozadí. Pokud chcete vynutit šifrování objektu blob, který se vytvořil před 20. října 2017, můžete objekt BLOB přepsat. Informace o tom, jak zjistit stav šifrování objektu blob, najdete v tématu [ověření stavu šifrování objektu BLOB](../blobs/storage-blob-encryption-status.md).

Další informace o kryptografických modulech podkladových Azure Storage šifrování najdete v tématu [kryptografické rozhraní API: další generace](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

Informace o šifrování a správě klíčů pro Azure Managed disks najdete v tématu [šifrování Azure Managed](../../virtual-machines/windows/disk-encryption.md) disks na straně serveru pro virtuální počítače s Windows nebo pro [šifrování na straně serveru Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) pro virtuální počítače se systémem Linux.

## <a name="about-encryption-key-management"></a>O správě šifrovacích klíčů

Data v novém účtu úložiště se ve výchozím nastavení šifrují pomocí klíčů spravovaných Microsoftem. Můžete i nadále spoléhat na klíče spravované Microsoftem pro šifrování vašich dat, nebo můžete spravovat šifrování pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování pomocí vlastních klíčů, máte dvě možnosti. Můžete použít buď typ správy klíčů, nebo obojí:

- Můžete zadat *klíč spravovaný zákazníkem* Azure Key Vault, který se má použít pro šifrování a dešifrování dat v úložišti objektů BLOB a ve službě soubory Azure. <sup>1.2</sup> . Další informace o klíčích spravovaných zákazníkem najdete v tématu [použití klíčů spravovaných zákazníkem se Azure Key Vault ke správě šifrování Azure Storage](encryption-customer-managed-keys.md).
- Můžete zadat *klíč poskytnutý zákazníkem* pro operace BLOB Storage. Klient, který vytváří požadavek na čtení nebo zápis proti úložišti objektů blob, může do žádosti přidat šifrovací klíč a získat tak podrobné řízení způsobu, jakým se data objektů BLOB šifrují a dešifrují. Další informace o klíčích poskytovaných zákazníkem najdete v tématu [poskytnutí šifrovacího klíče pro požadavek na úložiště objektů BLOB](encryption-customer-provided-keys.md).

Následující tabulka porovnává možnosti správy klíčů pro Azure Storage šifrování.

| Parametr správy klíčů | Klíče spravované Microsoftem | Klíče spravované zákazníkem | Klíče poskytované zákazníky |
|--|--|--|--|
| Operace šifrování a dešifrování | Azure | Azure | Azure |
| Podporované služby Azure Storage Services | Vše | BLOB Storage, soubory Azure<sup>1, 2</sup> | Blob Storage |
| Úložiště klíčů | Úložiště klíčů Microsoftu | Azure Key Vault | Vlastní úložiště klíčů zákazníka |
| Zodpovědnost za střídání klíčů | Partnerský vztah Microsoftu | Customer (Zákazník) | Customer (Zákazník) |
| Řízení klíčů | Partnerský vztah Microsoftu | Customer (Zákazník) | Customer (Zákazník) |

<sup>1</sup> informace o vytvoření účtu, který podporuje použití klíčů spravovaných zákazníkem s úložištěm Queue, najdete v tématu [Vytvoření účtu, který podporuje klíče spravované zákazníkem pro fronty](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> informace o vytvoření účtu, který podporuje použití klíčů spravovaných zákazníkem s tabulkovým úložištěm, najdete v tématu [Vytvoření účtu, který podporuje klíče spravované zákazníkem pro tabulky](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

> [!NOTE]
> Klíče spravované Microsoftem se podle požadavků na dodržování předpisů otočí odpovídajícím způsobem. Pokud máte specifické požadavky na střídání klíčů, společnost Microsoft doporučuje přejít na klíče spravované zákazníkem, abyste mohli snadno spravovat a auditovat své otáčení.

## <a name="encryption-scopes-for-blob-storage-preview"></a>Obory šifrování pro úložiště objektů BLOB (Preview)

Ve výchozím nastavení je účet úložiště zašifrovaný pomocí klíče, který je vymezený na účet úložiště. Pomocí klíčů spravovaných Microsoftem nebo klíčů spravovaných zákazníkem, které jsou uložené v Azure Key Vault, můžete chránit a řídit přístup ke klíči, který data šifruje.

Obory šifrování umožňují volitelně spravovat šifrování na úrovni kontejneru nebo jednotlivého objektu BLOB. Obory šifrování můžete použít k vytvoření zabezpečených hranic mezi daty, která se nacházejí ve stejném účtu úložiště, ale patří různým zákazníkům.

Pro účet úložiště můžete vytvořit jeden nebo víc rozsahů šifrování pomocí poskytovatele prostředků Azure Storage. Při vytváření oboru šifrování určíte, jestli je obor chráněný pomocí klíče spravovaného společností Microsoft, nebo pomocí klíče spravovaného zákazníkem, který je uložený v Azure Key Vault. Různé obory šifrování ve stejném účtu úložiště můžou používat klíče spravované Microsoftem nebo zákazníky.

Po vytvoření oboru šifrování můžete zadat obor šifrování pro požadavek na vytvoření kontejneru nebo objektu BLOB. Další informace o tom, jak vytvořit rozsah šifrování, najdete v tématu [Vytvoření a Správa oborů šifrování (Preview)](../blobs/encryption-scope-manage.md).

> [!NOTE]
> Obory šifrování nejsou podporovány v geograficky redundantním úložišti s přístupem pro čtení (RA-GRS) a v geograficky redundantním úložišti s přístupem pro čtení (RA-GZRS) ve verzi Preview.

> [!IMPORTANT]
> Obory šifrování ve verzi Preview jsou určené jenom pro neprodukční použití. Smlouvy o úrovni produkčních služeb (SLA) nejsou aktuálně k dispozici.
>
> Chcete-li se vyhnout neočekávaným nákladům, je nutné zakázat všechny obory šifrování, které aktuálně nepotřebujete.

### <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Vytvoření kontejneru nebo objektu BLOB s rozsahem šifrování

Objekty blob, které jsou vytvořeny v oboru šifrování, jsou šifrovány klíčem zadaným pro daný obor. Při vytváření objektu blob můžete určit rozsah šifrování pro jednotlivý objekt blob, nebo můžete při vytváření kontejneru určit výchozí rozsah šifrování. Když je na úrovni kontejneru zadaný výchozí rozsah šifrování, všechny objekty BLOB v tomto kontejneru se šifrují pomocí klíče přidruženého k výchozímu oboru.

Když vytvoříte objekt BLOB v kontejneru, který má výchozí obor šifrování, můžete zadat rozsah šifrování, který přepíše výchozí rozsah šifrování, pokud je kontejner nakonfigurovaný tak, aby povoloval přepsání výchozího oboru šifrování. Pokud chcete zabránit přepsání výchozího oboru šifrování, nakonfigurujte kontejner tak, aby odepřel přepsání pro jednotlivý objekt BLOB.

Operace čtení u objektu blob, který patří do oboru šifrování, jsou transparentní, pokud rozsah šifrování není zakázán.

### <a name="disable-an-encryption-scope"></a>Zakázání oboru šifrování

Když zakážete rozsah šifrování, všechny následné operace čtení nebo zápisu provedené s oborem šifrování selžou s kódem chyby HTTP 403 (zakázáno). Pokud znovu povolíte rozsah šifrování, operace čtení a zápisu budou normálně pokračovat.

Pokud je zakázaný obor šifrování, už se vám neúčtují. Zakažte všechny obory šifrování, které nepotřebujete, aby nedocházelo k zbytečným poplatkům.

Pokud je váš obor šifrování chráněný pomocí klíčů spravovaných zákazníkem pro Azure Key Vault, můžete také odstranit přidružený klíč v trezoru klíčů, aby se tento obor šifrování zakázal. Mějte na paměti, že klíče spravované zákazníkem v Azure Key Vault jsou chráněny ochranou pomocí obnovitelného odstranění a vyprázdnění a odstraněný klíč podléhá chování definovanému pro tyto vlastnosti. Další informace najdete v následujících tématech v dokumentaci k Azure Key Vault:

- [Použití obnovitelného odstranění s využitím PowerShellu](../../key-vault/general/soft-delete-powershell.md)
- [Jak používat obnovitelné odstranění pomocí rozhraní příkazového řádku](../../key-vault/general/soft-delete-cli.md)

> [!NOTE]
> Rozsah šifrování není možné odstranit.

## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault?](../../key-vault/general/overview.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage na webu Azure Portal](storage-encryption-keys-portal.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage v PowerShellu](storage-encryption-keys-powershell.md)
- [Konfigurace klíčů spravovaných zákazníkem pro šifrování služby Azure Storage v Azure CLI](storage-encryption-keys-cli.md)
