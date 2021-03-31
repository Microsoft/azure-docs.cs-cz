---
title: Migrace na řízení přístupu na základě role v Azure | Microsoft Docs
description: Naučte se migrovat zásady přístupu do trezoru do rolí Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: e7a8fd53e78e1aeab9db5af0432d0c3f1d786823
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100526948"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>Migrace ze zásad přístupu k trezoru na model oprávnění řízení přístupu na základě role Azure

Model zásad přístupu k trezoru je stávající autorizační systém integrovaný v Key Vault, který poskytuje přístup k klíčům, tajným klíčům a certifikátům. Přístup můžete řídit přiřazením individuálních oprávnění objektu zabezpečení (uživatele, skupiny, instančního objektu, spravované identity) v oboru Key Vault. 

Řízení přístupu na základě role Azure (Azure RBAC) je autorizační systém založený na [Azure Resource Manager](../../azure-resource-manager/management/overview.md) , který poskytuje jemně odstupňovanou správu prostředků Azure. Pomocí služby Azure RBAC řídíte přístup k prostředkům tím, že vytvoříte přiřazení rolí, které se skládá ze tří prvků: objekt zabezpečení, definice role (předdefinovaná sada oprávnění) a obor (skupina prostředků nebo individuální prostředek). Další informace najdete v tématu [řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md).

Před migrací do Azure RBAC je důležité porozumět jeho výhodám a omezením.

Výhody klíčových výhod Azure RBAC přes zásady přístupu do trezoru:
- Poskytuje model jednotného řízení přístupu pro prostředky Azure – stejné rozhraní API napříč službami Azure.
- Centralizovaná správa přístupu pro správce – Správa všech prostředků Azure v jednom zobrazení
- Integrace s [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) pro řízení přístupu na základě času
- Odepřít přiřazení – schopnost vyloučit objekt zabezpečení v konkrétním rozsahu. Informace najdete v tématu [Vysvětlení přiřazení Azure Deny](../../role-based-access-control/deny-assignments.md) .

Nevýhody Azure RBAC:
- Latence u přiřazení rolí – použití přiřazení role může trvat několik minut. Zásady přístupu k trezoru se přiřazují okamžitě.
- Omezený počet přiřazení rolí – 2000 přiřazení rolí v rámci předplatného a za 1024 zásad přístupu na Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Zásady přístupu k mapování rolí Azure

Služba Azure RBAC má několik předdefinovaných rolí Azure, které můžete přiřadit uživatelům, skupinám, objektům služby a spravovaným identitám. Pokud předdefinované role nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní [vlastní role Azure](../../role-based-access-control/custom-roles.md).

Key Vault předdefinované role pro klíče, certifikáty a správu přístupu k tajným klíčům:
- Správce Key Vault
- Čtecí modul Key Vault
- Key Vault Certificate důstojník
- Key Vault kryptografický pracovník
- Key Vault kryptografický uživatel
- Uživatel šifrování Key Vault šifrovací služby
- Úředník Key Vault tajných klíčů
- Uživatel Key Vault tajných klíčů

Další informace o existujících předdefinovaných rolích najdete v tématu [předdefinované role Azure](../../role-based-access-control/built-in-roles.md) .

Zásady přístupu k trezoru se dají přiřadit jednotlivě vybraným oprávněním nebo pomocí předdefinovaných šablon oprávnění.

Předdefinované šablony oprávnění k zásadám přístupu:
- Správa klíčů, tajných klíčů a certifikátů
- Správa klíčů & tajných kódů
- Správa certifikátů tajných &
- Správa klíčů
- Správa tajných klíčů
- Správa certifikátů
- Konektor SQL Serveru
- Azure Data Lake Storage nebo Azure Storage
- Azure Backup
- Klíč zákazníka Exchange Online
- Klíč zákazníka SharePointu Online
- Azure Information BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Šablony zásad přístupu k mapování rolí Azure
| Šablona zásad přístupu | Operace | Role Azure |
| --- | --- | --- |
| Správa klíčů, tajných klíčů a certifikátů | Klíče: všechny operace <br>Certifikáty: všechny operace<br>Tajné kódy: všechny operace | Správce Key Vault |
| Správa klíčů & tajných kódů | Klíče: všechny operace <br>Tajné kódy: všechny operace| Key Vault kryptografický pracovník <br> Úředník Key Vault tajných klíčů |
| Správa certifikátů tajných & | Certifikáty: všechny operace <br>Tajné kódy: všechny operace| Key Vault úřední certifikát <br> Úředník Key Vault tajných klíčů|
| Správa klíčů | Klíče: všechny operace| Key Vault kryptografický pracovník|
| Správa tajných klíčů | Tajné kódy: všechny operace| Úředník Key Vault tajných klíčů|
| Správa certifikátů | Certifikáty: všechny operace | Key Vault úřední certifikát|
| Konektor SQL Serveru | Klíče: získání, seznam, zabalení klíče, rozbalení klíče | Uživatel šifrování Key Vault šifrovací služby|
| Azure Data Lake Storage nebo Azure Storage | Klíče: získání, vypsání, rozbalení klíče | –<br> Vyžaduje se vlastní role.|
| Azure Backup | Klíče: získání, seznam, zálohování<br> Certifikát: získání, seznam, zálohování | –<br> Vyžaduje se vlastní role.|
| Klíč zákazníka Exchange Online | Klíče: získání, seznam, zabalení klíče, rozbalení klíče | Uživatel šifrování Key Vault šifrovací služby|
| Klíč zákazníka Exchange Online | Klíče: získání, seznam, zabalení klíče, rozbalení klíče | Uživatel šifrování Key Vault šifrovací služby|
| Azure Information BYOK | Klíče: získat, dešifrovat, podepsat | –<br>Vyžaduje se vlastní role.|


## <a name="assignment-scopes-mapping"></a>Mapování oborů přiřazení  

Azure RBAC pro Key Vault umožňuje přiřazení rolí v následujících oborech:
- Skupina pro správu
- Předplatné
- Skupina prostředků
- Prostředek Key Vault
- Jednotlivý klíč, tajný klíč a certifikát

Model oprávnění zásad přístupu k trezoru je omezený na přiřazování zásad pouze na Key Vault úrovni prostředků, což 

Obecně je vhodné mít jeden Trezor klíčů na každou aplikaci a spravovat přístup na úrovni trezoru klíčů. Existují scénáře, kdy Správa přístupu v jiných oborech může zjednodušit správu přístupu.

- * * Infrastruktura, správci zabezpečení a operátoři: Správa skupiny trezorů klíčů ve skupině pro správu, na úrovni předplatného nebo skupiny prostředků se zásadami přístupu trezoru vyžaduje zachování zásad pro každý Trezor klíčů. Azure RBAC umožňuje vytvořit jedno přiřazení role na skupinu pro správu, předplatné nebo skupinu prostředků. Toto přiřazení se bude vztahovat na všechny nové trezory klíčů vytvořené v rámci stejného oboru. V tomto scénáři se doporučuje použít Privileged Identity Management s přístupem k programu za běhu za účelem poskytnutí trvalého přístupu.
 
- * * Aplikace: existují scénáře, kdy aplikace potřebuje sdílet tajný klíč s jinou aplikací. Pomocí zásad přístupu trezoru se musí vytvořit oddělený Trezor klíčů, aby nedošlo k přístupu ke všem tajným klíčům. Azure RBAC umožňuje přiřadit roli k oboru pro jednotlivý tajný klíč místo toho použití trezoru s jedním klíčem.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Zásady přístupu trezoru k migračním krokům Azure RBAC
Existuje mnoho rozdílů mezi modelem oprávnění zásad přístupu k Azure RBAC a trezoru. Aby se předešlo výpadkům při migraci, doporučujeme postupovat níže.
 
1. **Identifikujte a přiřadíte role**: Identifikujte předdefinované role založené na tabulce mapování výše a v případě potřeby vytvořte vlastní role. Přiřaďte role v oborech na základě pokynů pro mapování oborů. Další informace o tom, jak přiřadit role k trezoru klíčů, najdete v článku [poskytnutí přístupu k Key Vault pomocí řízení přístupu na základě role v Azure](rbac-guide.md) .
1. **Ověřit přiřazení rolí**: aby se rozšířila přiřazení rolí v Azure RBAC, může to trvat několik minut. Návod, jak kontrolovat přiřazení rolí, najdete v tématu [přiřazení rolí v oboru](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) .
1. **Konfigurace monitorování a upozorňování na Trezor klíčů**: je důležité povolit upozorňování protokolování a nastavení pro zakázané výjimky přístupu. Další informace najdete v tématu [monitorování a upozorňování na Azure Key Vault](./alert.md)
1. **Nastavení modelu oprávnění řízení přístupu založeného na rolích Azure na Key Vault**: povolení modelu oprávnění pro Azure RBAC bude mít za následek zrušení platnosti všech stávajících zásad přístupu. Pokud dojde k chybě, model oprávnění se dá přepnout zpátky se všemi existujícími zásadami přístupu beze změny.

> [!NOTE]
> Změna modelu oprávnění vyžaduje oprávnění "Microsoft. Authorization/roleAssignments/Write", která je součástí rolí [vlastník](../../role-based-access-control/built-in-roles.md#owner) a [Správce přístupu uživatele](../../role-based-access-control/built-in-roles.md#user-access-administrator) . Role správce pro klasický odběr, jako je správce služeb a spolusprávce, se nepodporují.

> [!NOTE]
> Když je povolený model oprávnění Azure RBAC, všechny skripty, které se pokusí aktualizovat zásady přístupu, selžou. Je důležité tyto skripty aktualizovat, aby používaly Azure RBAC.

## <a name="troubleshooting"></a>Řešení potíží
-  Přiřazení role nefunguje po několika minutách – existují situace, kdy přiřazení rolí může trvat delší dobu. Je důležité napsat logiku opakování v kódu pro pokrytí těchto případů.
- Přiřazení rolí zmizelo, když se Key Vault odstranila (obnovitelné odstranění) a obnovila se – v současné době se jedná o omezení funkce obnovitelného odstranění napříč všemi službami Azure. Po obnovení je nutné znovu vytvořit všechna přiřazení rolí.    

## <a name="learn-more"></a>Další informace

- [Přehled Azure RBAC](../../role-based-access-control/overview.md)
- [Kurz pro vlastní role](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)