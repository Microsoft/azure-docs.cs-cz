---
title: Co je Azure Key Vault? | Dokumentace Microsoftu
description: Zjistěte, jak Azure Key Vault chrání kryptografické klíče a tajné klíče, které používají cloudové aplikace a služby.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 14eda137d386146d96b6b9aa54e1ed57021db19d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432135"
---
# <a name="azure-key-vault-basic-concepts"></a>Základní koncepty azure key vaultu

Azure Key Vault je nástroj pro zabezpečené ukládání tajných klíčů a přístup k nim. Tajný klíč je cokoli, k čemu chcete pečlivě kontrolovat přístup, třeba klíče rozhraní API, hesla nebo certifikáty. Trezor je logická skupina tajných kódů.

Zde jsou další důležité pojmy:

- **Tenant:** Tenant je organizace, která vlastní a spravuje konkrétní instanci cloudových služeb Microsoftu. Nejčastěji se používá k odkazování na sadu služeb Azure a Office 365 pro organizaci.

- **Vlastník trezoru:** Vlastník trezoru může vytvořit trezor klíčů a získat k němu úplný přístup a kontrolu. Vlastník trezoru může také nastavit auditování a protokolování toho, kdo získává přístup ke klíčům a tajným klíčům. Správci můžou řídit životní cyklus klíčů. Můžou přejít na novou verzi klíče, zálohovat ho a provádět související úlohy.

- **Uživatel trezoru:** Uživatel trezoru může provádět akce s prostředky uvnitř trezoru, pokud mu vlastník trezoru udělí uživatelský přístup. Dostupné akce závisí na udělených oprávněních.

- **Prostředek:** Prostředek je spravovatelná položka, která je k dispozici prostřednictvím Azure. Běžné příklady jsou virtuální počítač, účet úložiště, webová aplikace, databáze a virtuální síť. Je jich mnohem víc.

- **Skupina prostředků:** Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků.

- **Instanční objekt**: Instanční objekt Azure je identita zabezpečení, kterou uživatelem vytvořené aplikace, služby a automatizační nástroje používají pro přístup ke konkrétním prostředkům Azure. Představte si to jako "identitu uživatele" (uživatelské jméno a heslo nebo certifikát) s konkrétní rolí a přísně řízenými oprávněními. Na rozdíl od obecné identity uživatele instanční objekt potřebuje mít možnost provádět jenom určité akce. Zlepšuje zabezpečení, pokud mu udělíte pouze minimální úroveň oprávnění, kterou potřebuje k provádění svých úkolů správy.

- [Azure Active Directory (Azure AD):](../../active-directory/active-directory-whatis.md) Azure AD je služba Active Directory pro tenanta. Každý adresář má jednu nebo víc domén. K jednomu adresáři se dá přidružit několik předplatných, ale jenom jeden tenant.

- **ID tenanta Azure:** ID tenanta představuje jedinečný způsob, jak identifikovat instanci služby Azure AD v rámci předplatného Azure.

- **Spravované identity**: Azure Key Vault poskytuje způsob, jak bezpečně ukládat přihlašovací údaje a další klíče a tajné klíče, ale váš kód se musí ověřit do trezoru klíčů, aby je načetl. Použití spravované identity usnadňuje řešení tohoto problému tím, že služby Azure automaticky spravované identity ve službě Azure AD. Tuto identitu můžete použít k ověření ve službě Key Vault nebo jakékoli jiné službě, která podporuje ověřování Azure AD, aniž by váš kód obsahoval přihlašovací údaje. Další informace najdete na následujícím obrázku a [přehled spravovaných identit pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagram fungování spravovaných identit pro prostředky Azure](../media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Chcete-li provést všechny operace s trezorem klíčů, musíte se nejprve ověřit. Trezor klíčů lze ověřit třemi způsoby:

- [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md): Když nasadíte aplikaci na virtuální můloží v Azure, můžete přiřadit identitu k virtuálnímu počítači, který má přístup k trezoru klíčů. Identity můžete také přiřadit [k jiným prostředkům Azure](../../active-directory/managed-identities-azure-resources/overview.md). Výhodou tohoto přístupu je, že aplikace nebo služba nespravuje otočení prvního tajného klíče. Azure automaticky otočí identitu. Tento přístup doporučujeme jako osvědčený postup. 
- **Instanční objekt a certifikát**: Můžete použít instanční objekt a přidružený certifikát, který má přístup k trezoru klíčů. Tento přístup nedoporučujeme, protože vlastník aplikace nebo vývojář musí certifikát otočit.
- **Instanční objekt a tajný klíč**: Přestože můžete použít instanční objekt a tajný klíč k ověření trezoru klíčů, nedoporučujeme jej. Je těžké automaticky otočit tajný klíč bootstrapu, který se používá k ověření trezoru klíčů.


## <a name="key-vault-roles"></a>Role služby Key Vault

Následující tabulka vám pomůže lépe porozumět tomu, jak může Key Vault pomoci splnit potřeby vývojářů a správců zabezpečení.

| Role | Popis problému | Vyřešeno Azure Key Vault |
| --- | --- | --- |
| Vývojář aplikace Azure |"Chci napsat aplikaci pro Azure, která používá klíče pro podepisování a šifrování. Ale chci, aby tyto klíče byly externí z mé aplikace tak, aby řešení bylo vhodné pro aplikaci, která je geograficky distribuována. <br/><br/>Chci, aby tyto klíče a tajné klíče byly chráněné, bez nutnosti psát vlastní kód. Také chci, aby tyto klíče a tajemství byly pro mě snadno použitelné z mých aplikací s optimálním výkonem." |√ Klíče jsou uložené v trezoru, a když je potřeba, volají se identifikátorem URI.<br/><br/> √ Klíče jsou chráněné systémem Azure pomocí standardních algoritmů, délek klíčů a modulů hardwarového zabezpečení.<br/><br/> √ Klíče se zpracovávají v modulech HSM umístěných ve stejných datových centrech jako aplikace. Tato metoda poskytuje větší spolehlivost a nižší latenci, než kdyby byly klíče umístěné v samostatném umístění, například místně. |
| Vývojář softwaru jako služby (SaaS) |"Nechci odpovědnost nebo potenciální odpovědnost za klíče a tajemství nájemníků mých zákazníků. <br/><br/>Chci, aby zákazníci vlastnili a spravovali své klíče, abych se mohl soustředit na to, co umím nejlépe, což je poskytování základních softwarových funkcí." |√ Zákazníci můžou svoje klíče importovat do systému Azure a spravovat je. Když aplikace SaaS potřebuje provádět kryptografické operace pomocí klíčů zákazníků, trezor klíčů provádí tyto operace jménem aplikace. Aplikace nevidí klíče zákazníků. |
| Ředitel pro bezpečnost |"Chci vědět, že naše aplikace splňují systém zabezpečení s požadavky zabezpečení 140-2 úrovně 2 pro bezpečnou správu klíčů. <br/><br/>Chci se ujistit, že moje organizace má kontrolu nad životním cyklem klíčů a může monitorovat jejich využití. <br/><br/>A i když používáme více služeb a prostředků Azure, chci spravovat klíče z jednoho místa v Azure." |√ Moduly HMS jsou ověřené podle standardu FIPS 140-2 Level 2.<br/><br/>√ Key Vault je navržený tak, aby společnost Microsoft vaše klíče neznala ani neextrahovala.<br/><br/>√ Využití klíčů se protokoluje téměř v reálném čase.<br/><br/>√ Trezor poskytuje jednotné rozhraní – bez ohledu na to, kolik trezorů v Azure máte, které oblasti podporují a které aplikace je používají. |

Trezory klíčů může vytvářet a používat každý, kdo má předplatné Azure. Přestože key vault přináší výhody vývojářům a správcům zabezpečení, může být implementován a spravován správcem organizace, který spravuje další služby Azure. Tento správce se například může přihlásit pomocí předplatného Azure, vytvořit trezor pro organizaci, do které chcete ukládat klíče, a pak být zodpovědný za provozní úkoly, jako jsou tyto:

- Vytvoření nebo import klíče nebo tajného klíče
- Odvolání nebo odstranění klíče nebo tajného klíče
- Autorizace uživatelů nebo aplikací k přístupu k trezoru klíčů, aby potom mohli spravovat nebo používat jeho klíče a tajné klíče
- Konfigurace používání klíčů (například podepisování nebo šifrování)
- Sledování používání klíčů

Tento správce pak dává vývojářům IDENTIFIKÁTORY URI volat z jejich aplikací. Tento správce také poskytuje informace o protokolování využití klíčů správci zabezpečení. 

![Přehled fungování azure key vaultu][1]

Vývojáři také mohou spravovat klíče přímo, pomocí rozhraní API. Další informace najdete v [příručce pro vývojáře Key Vault](developers-guide.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [zabezpečit trezor](secure-your-key-vault.md)).

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault je dostupný ve většině oblastí. Další informace najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
