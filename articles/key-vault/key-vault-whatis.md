---
title: Co je Azure Key Vault? | Dokumenty Microsoft
description: Přečtěte si, jak Azure Key Vault chrání kryptografické klíče a tajné kódy, které využívají cloudové aplikace a služby.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 2786ec387d528e1593e2687d906060f8a2673a8c
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934476"
---
# <a name="what-is-azure-key-vault"></a>Co je Azure Key Vault?

Cloudové aplikace a služby využívají kryptografické klíče a tajné kódy k zajištění zabezpečení informací. Azure Key Vault chrání tyto klíče a tajné kódy. Při použití Key Vault můžete šifrovat ověřovací klíče, klíče účtu úložiště, šifrovací klíče dat, soubory PFX a hesla pomocí klíčů chráněných moduly hardwarového zabezpečení (HSM).

Key Vault pomáhá vyřešit následující problémy:

- **Správa tajných**kódů: Bezpečně ukládejte a důkladně kontrolujte přístup k tokenům, heslům, certifikátům, klíčům rozhraní API a dalším tajným klíčům.
- **Správa klíčů**: Vytváření a řízení šifrovacích klíčů, které šifrují vaše data 
- **Správa certifikátů**: Zřizování, Správa a nasazení veřejných a privátních certifikátů protokolu SSL/TLS (SSL (Secure Sockets Layer)/TLS) pro použití s Azure a interními připojenými prostředky. 
- **Ukládání tajných kódů s HSM**: K ochraně tajných klíčů a klíčů použijte HSM ověřený 2 na úrovni FIPS 140-2 úrovně 2.

## <a name="basic-concepts"></a>Základní koncepty

Azure Key Vault je nástroj pro zabezpečené ukládání tajných klíčů a přístup k nim. Tajný klíč je cokoli, k čemu chcete pečlivě kontrolovat přístup, třeba klíče rozhraní API, hesla nebo certifikáty. Trezor je logická skupina tajných kódů.

Tady jsou další důležité výrazy:

- **Tenant**: Tenant je organizace, která vlastní a spravuje konkrétní instanci cloudových služeb Microsoftu. Nejčastěji se používá k odkazování na sadu služeb Azure a Office 365 pro organizaci.

- **Vlastník trezoru**: Vlastník trezoru může vytvořit Trezor klíčů a získat úplný přístup a ovládat ho. Vlastník trezoru může také nastavit auditování a protokolování toho, kdo získává přístup ke klíčům a tajným klíčům. Správci můžou řídit životní cyklus klíčů. Můžou přejít na novou verzi klíče, zálohovat ho a provádět související úlohy.

- **Uživatel trezoru**: Uživatel trezoru může provádět akce s prostředky v trezoru klíčů, když vlastník trezoru udělí přístup k uživateli. Dostupné akce závisí na udělených oprávněních.

- **Prostředek**: Prostředek je spravovatelná položka, která je dostupná prostřednictvím Azure. Běžnými příklady jsou virtuální počítače, účet úložiště, Webová aplikace, databáze a virtuální síť. Existuje spousta dalších.

- **Skupina prostředků**: Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků.

- **Instanční objekt**: Instanční objekt Azure je bezpečnostní identita, kterou uživatelsky vytvořené aplikace, služby a nástroje pro automatizaci používají pro přístup ke konkrétním prostředkům Azure. Můžete si ho představit jako identitu uživatele (uživatelské jméno a heslo nebo certifikát) s určitou rolí a přísně řízenými oprávněními. Na rozdíl od obecné identity uživatele instanční objekt potřebuje mít možnost provádět jenom určité akce. Zvyšuje zabezpečení, pokud mu udělíte jenom minimální úroveň oprávnění, kterou potřebuje k provádění úloh správy.

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md): Azure AD je služba Active Directory pro tenanta. Každý adresář má jednu nebo víc domén. K jednomu adresáři se dá přidružit několik předplatných, ale jenom jeden tenant.

- **ID tenanta Azure**: ID tenanta je jedinečný způsob, jak identifikovat instanci Azure AD v rámci předplatného Azure.

- **Spravované identity**: Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů a dalších klíčů a tajných kódů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. Použití spravované identity usnadňuje řešení tohoto problému tím, že poskytuje službám Azure automaticky spravovanou identitu ve službě Azure AD. Tuto identitu můžete použít k ověření ve službě Key Vault nebo jakékoli jiné službě, která podporuje ověřování Azure AD, aniž by váš kód obsahoval přihlašovací údaje. Další informace najdete na následujícím obrázku a v tématu [Přehled spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagram principu fungování spravovaných identit pro prostředky Azure](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Ověřování
Abyste mohli provádět operace s Key Vault, musíte se nejdřív ověřit. Existují tři způsoby, jak ověřit Key Vault:

- [Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md): Když nasadíte aplikaci na virtuální počítač v Azure, můžete jí přiřadit identitu k vašemu virtuálnímu počítači, který má přístup k Key Vault. Můžete také přiřadit identity [jiným prostředkům Azure](../active-directory/managed-identities-azure-resources/overview.md). Výhodou tohoto přístupu je, že aplikace nebo služba nespravuje otočení prvního tajného klíče. Azure tuto identitu automaticky otočí. Jako osvědčený postup doporučujeme tento přístup. 
- **Instanční objekt a certifikát**: Můžete použít instanční objekt a přidružený certifikát, který má přístup k Key Vault. Tento přístup nedoporučujeme, protože vlastník aplikace nebo vývojář musí certifikát otočit.
- **Objekt služby a tajný kód**: I když můžete k ověřování Key Vault použít instanční objekt a tajný klíč, nedoporučujeme ho. Automatické střídání spouštěcího klíče, který se používá k ověření Key Vault, je obtížné.


## <a name="key-vault-roles"></a>Role služby Key Vault

Následující tabulka vám pomůže lépe porozumět tomu, jak může Key Vault pomoci splnit potřeby vývojářů a správců zabezpečení.

| Role | Popis problému | Vyřešeno Azure Key Vault |
| --- | --- | --- |
| Vývojář aplikace Azure |"Chci napsat aplikaci pro Azure, která používá klíče pro podepisování a šifrování. Ale chci, aby tyto klávesy byly externí z mé aplikace, aby bylo řešení vhodné pro geograficky distribuovanou aplikaci. <br/><br/>Chci, aby tyto klíče a tajné klíče byly chráněné, bez nutnosti psát vlastní kód. Také chci, aby pro mě byly tyto klíče a tajné klíče snadno použitelné v aplikacích a aby poskytovaly optimální výkon.“ |√ Klíče jsou uložené v trezoru, a když je potřeba, volají se identifikátorem URI.<br/><br/> √ Klíče jsou chráněné systémem Azure pomocí standardních algoritmů, délek klíčů a modulů hardwarového zabezpečení.<br/><br/> √ Klíče se zpracovávají v modulech HSM umístěných ve stejných datových centrech jako aplikace. Tato metoda poskytuje větší spolehlivost a nižší latenci, než kdyby byly klíče umístěné v samostatném umístění, například místně. |
| Vývojář softwaru jako služby (SaaS) |„Nechci nést odpovědnost za klientské klíče a tajné klíče zákazníků. <br/><br/>Chci, aby zákazníci sami vlastnili a spravovali své klíče, což mi umožní soustředit se na to, co umím nejlépe – poskytování základních softwarových funkcí.“ |√ Zákazníci můžou svoje klíče importovat do systému Azure a spravovat je. Když aplikace SaaS potřebuje provést kryptografické operace pomocí klíčů zákazníků, Key Vault tyto operace jménem aplikace. Aplikace klíče zákazníků nezná. |
| Ředitel pro bezpečnost |„Potřebuji vědět, že naše aplikace splňují standard FIPS 140-2 Level 2 pro moduly HMS, který zajišťuje bezpečnou správu klíčů. <br/><br/>Chci se ujistit, že moje organizace má kontrolu nad životním cyklem klíčů a může monitorovat jejich využití. <br/><br/>A přestože používáme více služeb a prostředků Azure, chci spravovat klíče z jednoho umístění v Azure.“ |√ Moduly HMS jsou ověřené podle standardu FIPS 140-2 Level 2.<br/><br/>√ Key Vault je navržený tak, aby společnost Microsoft vaše klíče neznala ani neextrahovala.<br/><br/>√ Využití klíčů se protokoluje téměř v reálném čase.<br/><br/>√ Trezor poskytuje jednotné rozhraní – bez ohledu na to, kolik trezorů v Azure máte, které oblasti podporují a které aplikace je používají. |

Trezory klíčů může vytvářet a používat každý, kdo má předplatné Azure. I když Key Vault výhody vývojářů a správců zabezpečení, dá se implementovat a spravovat správce organizace, který spravuje další služby Azure. Tento správce se například může přihlásit pomocí předplatného Azure, vytvořit trezor pro organizaci, do které se mají ukládat klíče, a pak zodpovídá za provozní úlohy, jako jsou tyto:

- Vytvoření nebo import klíče nebo tajného klíče
- Odvolání nebo odstranění klíče nebo tajného klíče
- Autorizace uživatelů nebo aplikací k přístupu k trezoru klíčů, aby potom mohli spravovat nebo používat jeho klíče a tajné klíče
- Konfigurace používání klíčů (například podepisování nebo šifrování)
- Sledování používání klíčů

Tento správce pak poskytuje vývojářům identifikátory URI pro volání ze svých aplikací. Tento správce taky poskytuje klíčovým informacím o protokolování použití Správce zabezpečení. 

![Přehled toho, jak Azure Key Vault funguje][1]

Vývojáři také mohou spravovat klíče přímo, pomocí rozhraní API. Další informace najdete v [příručce pro vývojáře Key Vault](key-vault-developers-guide.md).

## <a name="next-steps"></a>Další postup

Naučte se [zabezpečit svůj trezor](key-vault-secure-your-key-vault.md).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault je dostupný ve většině oblastí. Další informace najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
