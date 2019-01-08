---
title: Co je Azure Key Vault? -Azure Key Vault | Dokumentace Microsoftu
description: Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí Azure Key Vault můžou zákazníci šifrovat klíče a tajné klíče (např. ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat, soubory PFX a hesla) pomocí klíčů chráněných moduly hardwarového zabezpečení (HSM).
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: f3c198ab8a17df019f1735a9b62e27f1051f64c5
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076327"
---
# <a name="what-is-azure-key-vault"></a>Co je Azure Key Vault?

Služba Azure Key Vault vám pomůže vyřešit následující problémy:

- **Správa tajných kódů** -Azure Key Vault umožňuje bezpečně ukládat a důsledně řídit přístup k tokeny, hesla, certifikáty, klíče rozhraní API a dalších tajných kódů.
- **Správa klíčů** – Azure Key Vault se dá použít taky jako řešení pro správu klíčů. Azure Key Vault usnadňuje vytváření a správu šifrovacích klíčů sloužících k šifrování dat. 
- **Správa certifikátů** – Azure Key Vault je taky služba, která umožňuje snadné zřizování, správu a nasazování veřejných a privátních certifikátů SSL/TLS (Secure Sockets Layer/Transport Layer Security) pro použití s Azure a interními připojenými prostředky. 
- **Store tajných kódů se opírá o modulech hardwarového zabezpečení** -klíčů nebo tajných kódů se dají chránit buď softwarové, nebo podle standardu FIPS 140-2 úrovně 2 ověří moduly hardwarového zabezpečení.

## <a name="basic-concepts"></a>Základní koncepty

Azure Key Vault je nástroj pro zabezpečené ukládání tajných klíčů a přístup k nim. Tajný klíč je cokoli, k čemu chcete pečlivě kontrolovat přístup, třeba klíče rozhraní API, hesla nebo certifikáty. A **trezor** je logická skupina tajných kódů. Teď provedete jakékoli operace se službou Key Vault musíte nejprve k ověření do něj. 

V podstatě existují tři způsoby, jak ověření do služby Key Vault:

1. **Pomocí [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)**  (**doporučené a osvědčených postupů**): Když nasazujete aplikace na virtuálním počítači v Azure, můžete přiřadit identitu, která k virtuálnímu počítači, který má přístup do služby Key Vault. Můžete také přiřadit identity další prostředky azure, které jsou uvedeny [tady](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Výhodou tohoto přístupu je aplikace / služby není správu oběhu první tajného klíče. Azure automaticky otočí identitu. 
2. **Pomocí instančního objektu a certifikátu:** Druhou možností je použití instančního objektu a přidružený certifikát, který má přístup do služby Key Vault. Břemeno otáčení certifikát se na vlastníka aplikace nebo pro vývojáře a proto to nedoporučujeme.
3. **Pomocí instančního objektu a tajného kódu:** Ověření do služby Key Vault pomocí instančního objektu a tajného kódu je třetí možnost (ne upřednostňovanou možnost ověřování).

> [!NOTE]
> Je těžké automaticky otočit bootstrap tajný klíč použitý k ověření do služby Key Vault se nemá používat 3. jednu z uvedených možností.

Tady je několik klíčových pojmů:

- **Tenant**: Tenant je organizace, která vlastní a spravuje konkrétní instanci cloudovým službám Microsoftu. Nejčastěji určitým způsobem odkazuje na sadu služeb Azure nebo Office 365 dané organizace.
- **Vlastníka trezoru**: Vlastníka trezoru můžete vytvořit trezor klíčů a získat úplný přístup a kontrolu nad ním. Vlastník trezoru může také nastavit auditování a protokolování toho, kdo získává přístup ke klíčům a tajným klíčům. Správci můžou řídit životní cyklus klíčů. Můžou přejít na novou verzi klíče, zálohovat ho a provádět související úlohy.
- **Trezor příjemce**: Trezor příjemce může provádět akce s prostředky v trezoru klíčů při vlastníka trezoru uděluje přístup uživatelů. Dostupné akce závisí na udělených oprávněních.
- **Prostředek**: Prostředek je spravovatelná položka, která je k dispozici prostřednictvím Azure. Mezi běžné prostředky patří virtuální počítač, účet úložiště, webová aplikace, databáze nebo virtuální síť, ale existuje i mnoho dalších.
- **Skupina prostředků**: Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků.
- **Instanční objekt služby** -instanční objekt Azure je identita zabezpečení, používaná uživatelem vytvořené aplikace, služby a nástroje pro automatizaci pro přístup ke konkrétním prostředkům Azure. Můžete si ho představit jako identitu uživatele (uživatelské jméno a heslo nebo certifikát) s určitou rolí a přísně řízenými oprávněními. Na rozdíl od obecné identity uživatele instanční objekt potřebuje mít možnost provádět jenom určité akce. Zabezpečení můžete zvýšit tak, že mu přidělíte pouze minimální úroveň oprávnění k provádění úloh správy.
- **[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md)**: Azure AD je služba Active Directory pro tenanta. Každý adresář má jednu nebo víc domén. K jednomu adresáři se dá přidružit několik předplatných, ale jenom jeden tenant. 
- **ID klienta Azure**: ID tenanta je jedinečný způsob, jak identifikovat instanci služby Azure AD v rámci předplatného Azure.
- **Spravované identity pro prostředky Azure**: Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů a dalších klíčů a tajných kódů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. Použití spravované identity je řešení tohoto problému jednodušší tím, že automaticky spravovanou identitu služby Azure ve službě Azure AD. Tuto identitu můžete použít k ověření ve službě Key Vault nebo jakékoli jiné službě, která podporuje ověřování Azure AD, aniž by váš kód obsahoval přihlašovací údaje. Další informace najdete v tématu na obrázku níže a [spravovaných identit pro prostředky Azure přehled](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagram toho, jak spravovat identity pro práci s prostředky Azure](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Role služby Key Vault

Následující tabulka vám pomůže lépe porozumět tomu, jak může Key Vault pomoci splnit potřeby vývojářů a správců zabezpečení.

| Role | Popis problému | Vyřešeno Azure Key Vault |
| --- | --- | --- |
| Vývojář aplikace Azure |„Chci napsat aplikaci pro Azure, která k podepisování a šifrování používá klíče, ale tyto klíče musí být mimo aplikaci, aby bylo řešení vhodné pro geograficky distribuovanou aplikaci. <br/><br/>Chci, aby tyto klíče a tajné klíče byly chráněné, bez nutnosti psát vlastní kód. Také chci, aby pro mě byly tyto klíče a tajné klíče snadno použitelné v aplikacích a aby poskytovaly optimální výkon.“ |√ Klíče jsou uložené v trezoru, a když je potřeba, volají se identifikátorem URI.<br/><br/> √ Klíče jsou chráněné systémem Azure pomocí standardních algoritmů, délek klíčů a modulů hardwarového zabezpečení.<br/><br/> √ Klíče se zpracovávají v modulech HSM umístěných ve stejných datových centrech jako aplikace. Tato metoda poskytuje větší spolehlivost a nižší latenci, než kdyby byly klíče umístěné v samostatném umístění, například místně. |
| Vývojář softwaru jako služby (SaaS) |„Nechci nést odpovědnost za klientské klíče a tajné klíče zákazníků. <br/><br/>Chci, aby zákazníci sami vlastnili a spravovali své klíče, což mi umožní soustředit se na to, co umím nejlépe – poskytování základních softwarových funkcí.“ |√ Zákazníci můžou svoje klíče importovat do systému Azure a spravovat je. Když některá aplikace SaaS potřebuje provést kryptografické operace pomocí klíčů zákazníků, služba Key Vault tyto operace provede jménem aplikace. Aplikace klíče zákazníků nezná. |
| Ředitel pro bezpečnost |„Potřebuji vědět, že naše aplikace splňují standard FIPS 140-2 Level 2 pro moduly HMS, který zajišťuje bezpečnou správu klíčů. <br/><br/>Chci se ujistit, že moje organizace má kontrolu nad životním cyklem klíčů a může monitorovat jejich využití. <br/><br/>A přestože používáme více služeb a prostředků Azure, chci spravovat klíče z jednoho umístění v Azure.“ |√ Moduly HMS jsou ověřené podle standardu FIPS 140-2 Level 2.<br/><br/>√ Key Vault je navržený tak, aby společnost Microsoft vaše klíče neznala ani neextrahovala.<br/><br/>√ Využití klíčů se protokoluje téměř v reálném čase.<br/><br/>√ Trezor poskytuje jednotné rozhraní – bez ohledu na to, kolik trezorů v Azure máte, které oblasti podporují a které aplikace je používají. |

Trezory klíčů může vytvářet a používat každý, kdo má předplatné Azure. Přestože je Key Vault přínosný pro vývojáře a správce zabezpečení, může ho implementovat a spravovat libovolný správce organizace, který v organizaci spravuje ostatní služby Azure. Tento správce se například může přihlásit pomocí předplatného Azure, vytvořit pro organizaci trezor k ukládání klíčů a potom mít na starost provozní úlohy, jako například:

- Vytvoření nebo import klíče nebo tajného klíče
- Odvolání nebo odstranění klíče nebo tajného klíče
- Autorizace uživatelů nebo aplikací k přístupu k trezoru klíčů, aby potom mohli spravovat nebo používat jeho klíče a tajné klíče
- Konfigurace používání klíčů (například podepisování nebo šifrování)
- Sledování používání klíčů

Tento správce potom poskytne vývojářům identifikátory URI, které mohou volat z aplikací, a správcům zabezpečení poskytne informace o protokolování používání klíčů. 

! [Přehled služby Azure Key Vault] [1]

Vývojáři také mohou spravovat klíče přímo, pomocí rozhraní API. Další informace najdete v [příručce pro vývojáře Key Vault](key-vault-developers-guide.md).

## <a name="next-steps"></a>Další postup

Zjistěte, jak [zabezpečení trezoru](key-vault-secure-your-key-vault.md) 
 <!--Image references--> [1]:./media/key-vault-whatis/AzureKeyVault_overview.png Azure Key Vault je k dispozici ve většině oblastí. Další informace najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
