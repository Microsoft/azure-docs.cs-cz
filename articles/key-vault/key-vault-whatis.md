---
title: Co je Azure Key Vault? | Dokumenty Microsoft
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
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: barclayn
ms.openlocfilehash: 58bc3a582db23a48eedaaf67df7d20da9c42ded4
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603273"
---
# <a name="what-is-azure-key-vault"></a>Co je Azure Key Vault?

Azure Key Vault pomáhá řešit následující problémy
- **Správa tajných kódů** -Azure Key Vault umožňuje bezpečně ukládat a důsledně řídit přístup k tokeny, hesla, certifikáty, klíče rozhraní API a dalších tajných kódů
- **Správa klíčů** -Azure Key Vault slouží také jako řešení pro správu klíčů. Azure Key Vault usnadňuje vytváření a správu šifrovacích klíčů sloužících k šifrování dat. 
- **Správa certifikátů** – Azure Key Vault je také služba, která umožňuje snadné zřizování, spravovat a nasazovat veřejné a privátní certifikáty Secure Sockets Layer/Transport Layer Security (SSL/TLS) pro použití s Azure a vaší interní připojení prostředky. 
- **Moduly hardwarového zabezpečení** -klíčů nebo tajných kódů se dají chránit buď softwarové, nebo podle standardu FIPS 140-2 úrovně 2 ověří moduly hardwarového zabezpečení

## <a name="basic-concepts"></a>Základní koncepty

Azure Key Vault je nástroj pro zabezpečené ukládání tajných klíčů a přístup k nim. Tajný klíč je cokoli, k čemu chcete pečlivě kontrolovat přístup, třeba klíče rozhraní API, hesla nebo certifikáty. A **trezor** je logická skupina tajných kódů tady jsou některé klíčové pojmy:
- **Tenant:** Tenant je organizace, která vlastní a spravuje konkrétní instanci cloudových služeb Microsoftu. Nejčastěji určitým způsobem odkazuje na sadu služeb Azure nebo Office 365 dané organizace.
- **Vlastník trezoru:** Vlastník trezoru může vytvořit trezor klíčů a získat k němu úplný přístup a kontrolu. Vlastník trezoru může také nastavit auditování a protokolování toho, kdo získává přístup ke klíčům a tajným klíčům. Správci můžou řídit životní cyklus klíčů. Můžou přejít na novou verzi klíče, zálohovat ho a provádět související úlohy.
- **Uživatel trezoru:** Uživatel trezoru může provádět akce s prostředky uvnitř trezoru, pokud mu vlastník trezoru udělí uživatelský přístup. Dostupné akce závisí na udělených oprávněních.
- **Prostředek:** Prostředek je spravovatelná položka, která je k dispozici prostřednictvím Azure. Mezi běžné prostředky patří virtuální počítač, účet úložiště, webová aplikace, databáze nebo virtuální síť, ale existuje i mnoho dalších.
- **Skupina prostředků:** Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků.
- **Instanční objekt služby** -instančního objektu A mohli podívat, jako přihlašovacích údajů pro vaši aplikaci.
- **[Azure Active Directory (Azure AD):](../active-directory/active-directory-whatis.md)** Azure AD je služba Active Directory pro tenanta. Každý adresář má jednu nebo víc domén. K jednomu adresáři se dá přidružit několik předplatných, ale jenom jeden tenant. 
- **ID tenanta Azure:** ID tenanta představuje jedinečný způsob, jak identifikovat instanci služby Azure AD v rámci předplatného Azure.
- **Spravované identity pro prostředky Azure**: Azure Key Vault poskytuje způsob, jak bezpečně ukládat přihlašovací údaje a další klíče a tajné klíče, ale váš kód potřebuje ověřit do služby Key Vault je načítat. Použití spravované identity je řešení tohoto problému jednodušší tím, že automaticky spravovanou identitu služby Azure ve službě Azure AD. Tuto identitu můžete použít k ověření ve službě Key Vault nebo jakékoli jiné službě, která podporuje ověřování Azure AD, aniž by váš kód obsahoval přihlašovací údaje. Další informace najdete v tématu na obrázku níže a [spravovaných identit pro prostředky Azure přehled](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagram toho, jak spravovat identity pro prostředky Azure funguje](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Role služby Key Vault

Následující tabulka vám pomůže lépe porozumět tomu, jak může Key Vault pomoci splnit potřeby vývojářů a správců zabezpečení.

| Role | Popis problému | Vyřešeno Azure Key Vault |
| --- | --- | --- |
| Vývojář aplikace Azure |„Chci napsat aplikaci pro Azure, která k podepisování a šifrování používá klíče, ale tyto klíče musí být mimo aplikaci, aby bylo řešení vhodné pro geograficky distribuovanou aplikaci. <br/><br/>Chci, aby tyto klíče a tajné klíče byly chráněné, bez nutnosti psát vlastní kód. Také chci, aby pro mě byly tyto klíče a tajné klíče snadno použitelné v aplikacích a aby poskytovaly optimální výkon.“ |√ Klíče jsou uložené v trezoru, a když je potřeba, volají se identifikátorem URI.<br/><br/> √ Klíče jsou chráněné systémem Azure pomocí standardních algoritmů, délek klíčů a modulů hardwarového zabezpečení.<br/><br/> √ Klíče se zpracovávají v modulech HSM umístěných ve stejných datových centrech jako aplikace. Tato metoda poskytuje větší spolehlivost a nižší latenci, než kdyby byly klíče umístěné v samostatném umístění, například místně. |
| Vývojář softwaru jako služby (SaaS) |„Nechci nést odpovědnost za klientské klíče a tajné klíče zákazníků. <br/><br/>Chci, aby zákazníci sami vlastnili a spravovali své klíče, což mi umožní soustředit se na to, co umím nejlépe – poskytování základních softwarových funkcí.“ |√ Zákazníci můžou svoje klíče importovat do systému Azure a spravovat je. Když některá aplikace SaaS potřebuje provést kryptografické operace pomocí klíčů zákazníků, služba Key Vault tyto operace provede jménem aplikace. Aplikace klíče zákazníků nezná. |
| Ředitel pro bezpečnost |„Potřebuji vědět, že naše aplikace splňují standard FIPS 140-2 Level 2 pro moduly HMS, který zajišťuje bezpečnou správu klíčů. <br/><br/>Chci se ujistit, že moje organizace má kontrolu nad životním cyklem klíčů a může monitorovat jejich využití. <br/><br/>A přestože používáme více služeb a prostředků Azure, chci spravovat klíče z jednoho umístění v Azure.“ |√ Moduly HMS jsou ověřené podle standardu FIPS 140-2 Level 2.<br/><br/>√ Key Vault je navržený tak, aby společnost Microsoft vaše klíče neznala ani neextrahovala.<br/><br/>√ Využití klíčů se protokoluje téměř v reálném čase.<br/><br/>√ Trezor poskytuje jednotné rozhraní – bez ohledu na to, kolik trezorů v Azure máte, které oblasti podporují a které aplikace je používají. |

Trezory klíčů může vytvářet a používat každý, kdo má předplatné Azure. Přestože je Key Vault přínosný pro vývojáře a správce zabezpečení, může ho implementovat a spravovat libovolný správce organizace, který v organizaci spravuje ostatní služby Azure. Tento správce se například může přihlásit pomocí předplatného Azure, vytvořit pro organizaci trezor k ukládání klíčů a potom mít na starost provozní úlohy, jako například:

* Vytvoření nebo import klíče nebo tajného klíče
* Odvolání nebo odstranění klíče nebo tajného klíče
* Autorizace uživatelů nebo aplikací k přístupu k trezoru klíčů, aby potom mohli spravovat nebo používat jeho klíče a tajné klíče
* Konfigurace používání klíčů (například podepisování nebo šifrování)
* Sledování používání klíčů

Tento správce potom poskytne vývojářům identifikátory URI, které mohou volat z aplikací, a správcům zabezpečení poskytne informace o protokolování používání klíčů. 

![Přehled Azure Key Vault][1]

Vývojáři také mohou spravovat klíče přímo, pomocí rozhraní API. Další informace najdete v [příručce pro vývojáře Key Vault](key-vault-developers-guide.md).

## <a name="next-steps"></a>Další postup

Úvodní kurz pro správce najdete v tématu [Začínáme s Azure Key Vault](key-vault-get-started.md).

Další informace o protokolování využití služby Key Vault najdete v tématu [Protokolování v Azure Key Vault](key-vault-logging.md).

Další informace o používání klíčů a tajných klíčů se službou Azure Key Vault najdete v tématu [Informace o klíčích, tajných klíčích a certifikátech](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault je dostupný ve většině oblastí. Další informace najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
