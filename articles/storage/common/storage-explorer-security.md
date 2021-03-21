---
title: Průzkumník služby Azure Storage Průvodce zabezpečením | Microsoft Docs
description: Pokyny k zabezpečení pro Průzkumník služby Azure Storage
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: 283ec9999f9b4362035b6770383984efb0879d49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92783755"
---
# <a name="azure-storage-explorer-security-guide"></a>Průzkumník služby Azure Storage Průvodce zabezpečením

Průzkumník služby Microsoft Azure Storage umožňuje snadnou a zabezpečenou práci s Azure Storagemi daty v systémech Windows, macOS a Linux. Pomocí následujících pokynů můžete zajistit, aby vaše data zůstala chráněná.

## <a name="general"></a>Obecné

- **Vždy používejte nejnovější verzi Průzkumník služby Storage.** Verze Průzkumník služby Storage mohou obsahovat aktualizace zabezpečení. Udržování aktuálnosti pomáhá zajistit obecné zabezpečení.
- **Připojte se pouze k prostředkům, kterým důvěřujete.** Data, která stáhnete z nedůvěryhodných zdrojů, můžou být škodlivá a nahrávání dat do nedůvěryhodného zdroje může vést ke ztrátě nebo odcizení dat.
- **Pokud je to možné, použijte HTTPS.** Průzkumník služby Storage ve výchozím nastavení používá protokol HTTPS. Některé scénáře umožňují používat protokol HTTP, ale protokol HTTP by se měl používat jenom jako poslední možnost.
- **Zajistěte, aby lidé, kteří je potřebují, měli přidělená jenom potřebná oprávnění.** Vyhněte se neopatrnosti při udělování přístupu k vašim prostředkům všem uživatelům.
- **Při provádění kritických operací buďte opatrní.** Některé operace, například odstranění a přepsání, jsou nevratné a můžou způsobit ztrátu dat. Před spuštěním těchto operací se ujistěte, že pracujete se správnými prostředky.

## <a name="choosing-the-right-authentication-method"></a>Výběr správné metody ověřování

Průzkumník služby Storage poskytuje různé způsoby přístupu k prostředkům Azure Storage. Libovolná metoda, kterou si zvolíte, jsou naše doporučení.

### <a name="azure-ad-authentication"></a>Ověřování Azure AD

Nejjednodušší a nejbezpečnější způsob, jak získat přístup k prostředkům Azure Storage, je přihlásit se pomocí účtu Azure. Přihlášení používá ověřování Azure AD, které vám umožní:

- Poskytněte přístup konkrétním uživatelům a skupinám.
- Odvolejte přístup konkrétním uživatelům a skupinám kdykoli.
- Vynutila podmínky přístupu, jako je třeba vyžadování vícefaktorového ověřování.

Pokud je to možné, doporučujeme používat ověřování Azure AD.

Tato část popisuje dvě technologie založené na službě Azure AD, které se dají použít k zabezpečení prostředků úložiště.

#### <a name="azure-role-based-access-control-azure-rbac"></a>Řízení přístupu na základě role Azure (Azure RBAC)

[Řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) poskytuje podrobné řízení přístupu k prostředkům Azure. Role a oprávnění Azure je možné spravovat z Azure Portal.

Průzkumník služby Storage podporuje přístup Azure RBAC k účtům úložiště, objektům blob a frontám. Pokud potřebujete přístup ke sdíleným složkám nebo tabulkám, budete muset přiřadit role Azure, které udělí oprávnění k vypsání klíčů účtu úložiště.

#### <a name="access-control-lists-acls"></a>Seznamy ACL

[Seznamy řízení přístupu (ACL)](../blobs/data-lake-storage-access-control.md) umožňují řídit přístup na úrovni souborů a složek v adls Gen2 kontejnerech objektů BLOB. Seznamy ACL můžete spravovat pomocí Průzkumník služby Storage.

### <a name="shared-access-signatures-sas"></a>Sdílené přístupové podpisy (SAS)

Pokud nemůžete použít ověřování Azure AD, doporučujeme použít sdílené přístupové podpisy. Pomocí sdílených přístupových podpisů můžete:

- Poskytněte anonymní omezený přístup k zabezpečeným prostředkům.
- Pokud se vygeneruje ze zásad sdíleného přístupu (SAP), Odvolejte SAS přímo.

U sdílených přístupových podpisů ale nemůžete:

- Omezte, kdo může používat SAS. Platné přidružení zabezpečení může použít kdokoli, kdo ho má.
- Pokud se negeneruje ze zásad sdíleného přístupu (SAP), Odvolejte SAS.

Při použití SAS v Průzkumník služby Storage doporučujeme následující pokyny:

- **Omezte distribuci tokenů SAS a identifikátorů URI.** Pouze distribuujte tokeny SAS a identifikátory URI důvěryhodným jednotlivcům. Omezení distribuce SAS snižuje pravděpodobnost, že se SAS může zneužít.
- **Používejte jenom tokeny SAS a identifikátory URI z entit, které důvěřujete.**
- **Pokud je to možné, používejte zásady sdíleného přístupu (SAP) pro generování tokenů SAS a identifikátorů URI.** SAS založená na zásadách sdíleného přístupu je bezpečnější než úplné přidružení zabezpečení, protože je možné SAS odvolat odstraněním SAP.
- **Vygenerujte tokeny s minimálním přístupem k prostředkům a oprávněními.** Minimální oprávnění omezují potenciální škodu, kterou je možné provést v případě, že se SAS nepoužívá.
- **Vygenerujte tokeny, které jsou platné pouze po dobu potřebnou pro.** Krátká životnost je zvláště důležitá pro úplné SAS, protože neexistuje způsob, jak je odvolat po vygenerování.

> [!IMPORTANT]
> Při sdílení tokenů SAS a identifikátorů URI pro účely řešení potíží, jako jsou například žádosti o služby nebo zprávy o chybách, vždy redigování alespoň část podpisu SAS.

### <a name="storage-account-keys"></a>Klíče účtu úložiště

Klíče účtu úložiště udělují neomezený přístup ke službám a prostředkům v rámci účtu úložiště. Z tohoto důvodu doporučujeme omezit použití klíčů pro přístup k prostředkům v Průzkumník služby Storage. Místo toho použijte funkce služby Azure RBAC nebo SAS k poskytnutí přístupu.

Některé role Azure udělují oprávnění k načtení klíčů účtu úložiště. Uživatelé s těmito rolemi můžou efektivně obejít oprávnění udělená nebo zakázaná službou Azure RBAC. Toto oprávnění nedoporučujeme udělit, pokud není nutné.

Průzkumník služby Storage se pokusí použít klíče účtu úložiště, pokud jsou k dispozici, k ověření požadavků. Tuto funkci můžete zakázat v nastavení (**služby > účty úložiště > zakázání použití klíčů**). Některé funkce nepodporují službu Azure RBAC, jako je práce s klasickými účty úložiště. Tyto funkce stále vyžadují klíče a nejsou tímto nastavením ovlivněny.

Pokud pro přístup k prostředkům úložiště potřebujete použít klíče, doporučujeme následující pokyny:

- **Nesdílejte klíče účtu s kýmkoli.**
- **Považovat klíče účtu úložiště jako hesla.** Pokud je nutné, aby byly klíče přístupné, použijte řešení zabezpečeného úložiště, například [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

> [!NOTE]
> Pokud se domníváte, že jste klíč účtu úložiště sdíleli nebo distribuuje omylem, můžete vygenerovat nové klíče pro účet úložiště z Azure Portal.

### <a name="public-access-to-blob-containers"></a>Veřejný přístup k kontejnerům objektů BLOB

Průzkumník služby Storage umožňuje upravit úroveň přístupu vašich kontejnerů Azure Blob Storage. Neprivátní kontejnery objektů BLOB umožňují komukoli anonymní přístup pro čtení k datům v těchto kontejnerech.

Při povolování veřejného přístupu pro kontejner objektů BLOB doporučujeme následující pokyny:

- **Nepovolujte veřejný přístup ke kontejneru objektů blob, který může obsahovat potenciálně citlivá data.** Zajistěte, aby váš kontejner objektů BLOB byl bezplatný ze všech privátních dat.
- **Nenahrávat potenciálně citlivá data do kontejneru objektů BLOB s přístupem k objektu BLOB nebo kontejneru.** 

## <a name="next-steps"></a>Další kroky

- [Doporučení zabezpečení](../blobs/security-recommendations.md)