---
title: O spravované doméně zabezpečení HSM Azure
description: Přehled spravované domény zabezpečení HSM – sada základních přihlašovacích údajů potřebných k obnovení spravovaného modulu HSM
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 09/15/2020
ms.openlocfilehash: 37e2541d0b53c96fd3f85da31b2c0ce5b68b551a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90995542"
---
# <a name="about-the-managed-hsm-security-domain"></a>O spravované doméně zabezpečení HSM

Spravovaná doména zabezpečení HSM je sada základních přihlašovacích údajů, které jsou potřeba k obnovení spravovaného modulu HSM v případě havárie. Doména zabezpečení je vygenerována ve spravovaném hardwaru HSM a softwaru služby enclaves a představuje "vlastnictví" modulu HSM.

Každý spravovaný modul HSM musí mít doménu zabezpečení, která má fungovat. Když si vyžádáte nový spravovaný modul HSM, zřídí se, ale neaktivuje se, dokud nestáhnete doménu zabezpečení. Pokud je spravovaný modul HSM ve stavu zřizování, ale ne aktivovaný, je možné ho aktivovat dvěma způsoby:
- Stažení vaší domény zabezpečení je výchozí metoda a umožňuje bezpečné uložení domény zabezpečení, a to buď pro použití s jiným spravovaným modulem HSM, nebo k zotavení po celkové havárii.
- Nahrajte stávající doménu zabezpečení, kterou už máte, což vám umožní vytvořit víc spravovaných instancí HSM, které sdílejí stejnou doménu zabezpečení.

## <a name="download-your-security-domain"></a>Stažení domény zabezpečení

Pokud je spravovaný modul HSM zřízený, ale není aktivovaný, při stahování domény zabezpečení se zachytí základní přihlašovací údaje potřebné k obnovení z úplné ztráty veškerého hardwaru. Chcete-li stáhnout doménu zabezpečení, je nutné vytvořit alespoň 3 (maximum 10) párů klíčů RSA a odeslat tyto veřejné klíče službě při žádosti o stažení domény zabezpečení. Je také potřeba zadat minimální počet požadovaných klíčů (kvorum) k dešifrování domény zabezpečení v budoucnu. Spravovaný modul HSM inicializuje doménu zabezpečení a zašifruje je pomocí veřejných klíčů, které poskytnete pomocí [algoritmu sdílení tajného klíče Shamir](https://dl.acm.org/doi/10.1145/359168.359176). Objekt BLOB domény zabezpečení, který stáhnete, se dá dešifrovat, jenom když je k dispozici aspoň kvorum privátních klíčů. aby bylo zajištěno zabezpečení domény zabezpečení, je nutné privátní klíče bezpečně zabezpečit. Po dokončení stahování bude spravovaný modul HSM v aktivovaném stavu připravený k použití.  

> [!IMPORTANT]
> U úplného zotavení po havárii musíte mít doménu zabezpečení a kvorum privátních klíčů, které se použily k ochraně, a úplné zálohování HSM. Pokud dojde k výpadku domény zabezpečení nebo ke ztrátě klíčů RSA (privátní klíč) a k dispozici nejsou žádné spuštěné instance spravovaného modulu HSM, zotavení po havárii nebude možné.

## <a name="upload-a-security-domain"></a>Odeslat doménu zabezpečení

Pokud je spravovaný modul HSM zřízený, ale není aktivovaný, můžete zahájit proces obnovy domény zabezpečení. Spravovaný modul HSM vytvoří pár klíčů RSA a vrátí veřejný klíč. Pak můžete odeslat doménu zabezpečení do spravovaného modulu HSM. Před nahráním se musí klient (Azure CLI nebo PowerShell) zadat s minimálním počtem kvora privátních klíčů, které jste použili při stahování domény zabezpečení. Klient dešifruje doménu zabezpečení pomocí tohoto kvora a znovu ji zašifruje pomocí veřejného klíče, který jste stáhli při vyžádání obnovení. Po dokončení nahrávání bude spravovaný modul HSM v aktivovaném stavu.

## <a name="backup-and-restore-behavior"></a>Chování zálohování a obnovení

Zálohy (úplné zálohování nebo jediné zálohování klíčů) se dají úspěšně obnovit jenom v případě, že zdrojový modul HARDWAROVÉho zabezpečení (ve kterém se vytvořila záloha) a cílový spravovaný modul HSM (kde se záloha obnoví), bude sdílet stejnou doménu zabezpečení. V takovém případě doména zabezpečení také definuje pro každý spravovaný modul HSM kryptografickou hranici.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Přehled spravovaného HSM](overview.md)
- Další informace o [správě spravovaného modulu HSM pomocí Azure CLI](key-management.md)
- Projděte si spravované osvědčené [postupy modulu HSM](best-practices.md)
