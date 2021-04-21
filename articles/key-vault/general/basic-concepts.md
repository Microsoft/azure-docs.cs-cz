---
title: Co je Azure Key Vault? | Dokumentace Microsoftu
description: Přečtěte si, jak Azure Key Vault chrání kryptografické klíče a tajné kódy, které využívají cloudové aplikace a služby.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 863f98e643a7978856c03f5efe95736e6787f977
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814401"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault základní koncepty

Azure Key Vault je cloudová služba pro bezpečné ukládání a přístup k tajným klíčům. Tajný kód je cokoli, ke kterému chcete přísně řídit přístup, jako jsou klíče rozhraní API, hesla, certifikáty nebo kryptografické klíče. Služba Key Vault podporuje dva typy kontejnerů: trezory a fondy spravovaného modulu zabezpečení hardwaru (HSM). Trezory podporují ukládání softwaru a klíčů, tajných kódů a certifikátů, které jsou zálohovány modulem HSM. Spravované fondy HSM podporují jenom klíče zálohované modulem HSM. Úplné podrobnosti najdete v tématu [přehled Azure Key Vault REST API](about-keys-secrets-certificates.md) .

Tady jsou další důležité výrazy:

- **Tenant:** Tenant je organizace, která vlastní a spravuje konkrétní instanci cloudových služeb Microsoftu. Nejčastěji se používá k odkazování na sadu služeb Azure a Microsoft 365 pro organizaci.

- **Vlastník trezoru:** Vlastník trezoru může vytvořit trezor klíčů a získat k němu úplný přístup a kontrolu. Vlastník trezoru může také nastavit auditování a protokolování toho, kdo získává přístup ke klíčům a tajným klíčům. Správci můžou řídit životní cyklus klíčů. Můžou přejít na novou verzi klíče, zálohovat ho a provádět související úlohy.

- **Uživatel trezoru:** Uživatel trezoru může provádět akce s prostředky uvnitř trezoru, pokud mu vlastník trezoru udělí uživatelský přístup. Dostupné akce závisí na udělených oprávněních.

- **Spravované správce HSM**: uživatelům, kteří mají přiřazenou roli správce, má úplnou kontrolu nad spravovaným fondem HSM. Můžou vytvořit další přiřazení rolí, která budou delegovat řízený přístup jiným uživatelům.

- **Spravovaný kryptografický pracovník HSM/uživatel**: předdefinované role, které se obvykle přiřazují uživatelům nebo instančním objektům, které budou provádět kryptografické operace pomocí klíčů ve spravovaném modulu HSM. Kryptografický uživatel může vytvořit nové klíče, ale nemůže odstranit klíče.

- **Spravované šifrování kryptografické služby HSM**: Vestavěná role, která se obvykle přiřazuje identitě spravované služby (např. účet úložiště) pro šifrování neaktivních dat pomocí klíče spravovaného zákazníkem.

- **Prostředek:** Prostředek je spravovatelná položka, která je k dispozici prostřednictvím Azure. Běžnými příklady jsou virtuální počítače, účet úložiště, Webová aplikace, databáze a virtuální síť. Existuje spousta dalších.

- **Skupina prostředků:** Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků.

- **Objekt zabezpečení**: objekt zabezpečení Azure je identita zabezpečení, kterou uživatelsky vytvořené aplikace, služby a nástroje pro automatizaci používají pro přístup ke konkrétním prostředkům Azure. Můžete si ho představit jako identitu uživatele (uživatelské jméno a heslo nebo certifikát) s určitou rolí a přísně řízenými oprávněními. Objekt zabezpečení by měl na rozdíl od obecné identity uživatele vyžadovat pouze konkrétní věci. Zvyšuje zabezpečení, pokud mu udělíte jenom minimální úroveň oprávnění, kterou potřebuje k provádění úloh správy. Objekt zabezpečení, který se používá u aplikace nebo služby, se konkrétně nazývá **instanční objekt**.

- [Azure Active Directory (Azure AD):](../../active-directory/fundamentals/active-directory-whatis.md) Azure AD je služba Active Directory pro tenanta. Každý adresář má jednu nebo víc domén. K jednomu adresáři se dá přidružit několik předplatných, ale jenom jeden tenant.

- **ID tenanta Azure:** ID tenanta představuje jedinečný způsob, jak identifikovat instanci služby Azure AD v rámci předplatného Azure.

- **Spravované identity**: Azure Key Vault poskytuje způsob bezpečného ukládání přihlašovacích údajů a dalších klíčů a tajných kódů, ale váš kód se musí ověřit, aby se Key Vault načetl. Použití spravované identity usnadňuje řešení tohoto problému tím, že poskytuje službám Azure automaticky spravovanou identitu ve službě Azure AD. Tuto identitu můžete použít k ověření ve službě Key Vault nebo jakékoli jiné službě, která podporuje ověřování Azure AD, aniž by váš kód obsahoval přihlašovací údaje. Další informace najdete na následujícím obrázku a v tématu [Přehled spravovaných identit pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authentication"></a>Authentication
Abyste mohli provádět operace s Key Vault, musíte se nejdřív ověřit. Existují tři způsoby, jak ověřit Key Vault:

- [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md): když nasadíte aplikaci na virtuální počítač v Azure, můžete mu přiřadit identitu k vašemu virtuálnímu počítači, který má přístup k Key Vault. Můžete také přiřadit identity [jiným prostředkům Azure](../../active-directory/managed-identities-azure-resources/overview.md). Výhodou tohoto přístupu je, že aplikace nebo služba nespravuje otočení prvního tajného klíče. Azure tuto identitu automaticky otočí. Jako osvědčený postup doporučujeme tento přístup. 
- **Objekt služby a certifikát**: můžete použít instanční objekt a přidružený certifikát, který má přístup k Key Vault. Tento přístup nedoporučujeme, protože vlastník aplikace nebo vývojář musí certifikát otočit.
- **Instanční objekt a tajný kód**: i když můžete k ověření Key Vault použít instanční objekt a tajný klíč, nedoporučujeme ho. Automatické střídání spouštěcího klíče, který se používá k ověření Key Vault, je obtížné.


## <a name="key-vault-roles"></a>Role služby Key Vault

Následující tabulka vám pomůže lépe porozumět tomu, jak může Key Vault pomoci splnit potřeby vývojářů a správců zabezpečení.

| Role | Popis problému | Vyřešeno Azure Key Vault |
| --- | --- | --- |
| Vývojář aplikace Azure |"Chci napsat aplikaci pro Azure, která používá klíče pro podepisování a šifrování. Ale chci, aby tyto klávesy byly externí z mé aplikace, aby bylo řešení vhodné pro geograficky distribuovanou aplikaci. <br/><br/>Chci, aby tyto klíče a tajné klíče byly chráněné, bez nutnosti psát vlastní kód. Chci také, aby tyto klíče a tajné kódy byly snadno použitelné z mých aplikací s optimálním výkonem. " |√ Klíče jsou uložené v trezoru, a když je potřeba, volají se identifikátorem URI.<br/><br/> √ Klíče jsou chráněné systémem Azure pomocí standardních algoritmů, délek klíčů a modulů hardwarového zabezpečení.<br/><br/> √ Klíče se zpracovávají v modulech HSM umístěných ve stejných datových centrech jako aplikace. Tato metoda poskytuje větší spolehlivost a nižší latenci, než kdyby byly klíče umístěné v samostatném umístění, například místně. |
| Vývojář softwaru jako služby (SaaS) |"Nechci mít odpovědnost ani potenciální odpovědnost za klíče tenanta a tajné klíče pro moje zákazníky. <br/><br/>Chci, aby si zákazníci mohli vlastnit a spravovat svoje klíče, abyste se mohli soustředit na to, co je nejlepší, což poskytuje základní softwarové funkce. |√ Zákazníci můžou svoje klíče importovat do systému Azure a spravovat je. Když aplikace SaaS potřebuje provést kryptografické operace pomocí klíčů zákazníků, Key Vault tyto operace jménem aplikace. Aplikace nevidí klíče zákazníků. |
| Ředitel pro bezpečnost |"Chci vědět, že naše aplikace vyhovují standardu FIPS 140-2 úrovně 2 nebo FIPS 140-2 úrovně 3 HSM pro zabezpečenou správu klíčů. <br/><br/>Chci se ujistit, že moje organizace má kontrolu nad životním cyklem klíčů a může monitorovat jejich využití. <br/><br/>I když používáme více služeb a prostředků Azure, chci spravovat klíče z jednoho místa v Azure. " |√ Vyberte **trezory** pro standard FIPS 140-2 úrovně 2 HSM.<br/>√ Zvolit **spravované fondy HSM** pro ověření standardu FIPS 140-2 Level 3 HSM.<br/><br/>√ Key Vault je navržený tak, aby společnost Microsoft vaše klíče neznala ani neextrahovala.<br/>√ Využití klíčů se protokoluje téměř v reálném čase.<br/><br/>√ Trezor poskytuje jednotné rozhraní – bez ohledu na to, kolik trezorů v Azure máte, které oblasti podporují a které aplikace je používají. |

Trezory klíčů může vytvářet a používat každý, kdo má předplatné Azure. I když Key Vault výhody vývojářů a správců zabezpečení, dá se implementovat a spravovat správce organizace, který spravuje další služby Azure. Tento správce se například může přihlásit pomocí předplatného Azure, vytvořit trezor pro organizaci, do které se mají ukládat klíče, a pak zodpovídá za provozní úlohy, jako jsou tyto:

- Vytvoření nebo import klíče nebo tajného klíče
- Odvolání nebo odstranění klíče nebo tajného klíče
- Autorizace uživatelů nebo aplikací k přístupu k trezoru klíčů, aby potom mohli spravovat nebo používat jeho klíče a tajné klíče
- Konfigurace používání klíčů (například podepisování nebo šifrování)
- Sledování používání klíčů

Tento správce pak poskytuje vývojářům identifikátory URI pro volání ze svých aplikací. Tento správce taky poskytuje klíčovým informacím o protokolování použití Správce zabezpečení. 

![Přehled toho, jak Azure Key Vault funguje][1]

Vývojáři také mohou spravovat klíče přímo, pomocí rozhraní API. Další informace najdete v [příručce pro vývojáře Key Vault](developers-guide.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [Azure Key Vault funkcích zabezpečení](security-features.md).
- Přečtěte si, jak [zabezpečit spravované fondy HSM](../managed-hsm/access-control.md) .

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault je dostupný ve většině oblastí. Další informace najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
