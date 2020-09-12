---
title: Přehled zabezpečení
description: Bezpečnostní informace o serverech s podporou ARC Azure (Preview)
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 17641fab9933d9d6a60c2b21912f755acc01a6dd
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89447794"
---
# <a name="azure-arc-for-servers-preview-security-overview"></a>Přehled zabezpečení Azure ARC for Servers (Preview)

Tento článek popisuje konfiguraci zabezpečení a posouzení, které byste měli před nasazením serverů s podporou ARC Azure v podniku vyhodnotit.

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

Každý server s podporou ARC Azure má spravovanou identitu jako součást skupiny prostředků v rámci předplatného Azure, které představuje server, na kterém běží místně nebo jiné cloudové prostředí. Přístup k tomuto prostředku se řídí standardním [řízením přístupu na základě role Azure](../../role-based-access-control/overview.md). Na stránce [**Access Control (IAM)**](../../role-based-access-control/role-assignments-portal.md#access-control-iam) v Azure Portal můžete ověřit, kdo má přístup k serveru s podporou ARC Azure.

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Řízení přístupu k serveru s podporou ARC Azure" border="false" lightbox="./media/security-overview/access-control-page.png":::

Uživatelé a aplikace, kterým byl udělen přístup k prostředku prostřednictvím role [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) nebo správce, můžou provádět změny prostředku, včetně nasazení nebo odstranění [rozšíření](manage-vm-extensions.md) v počítači. Rozšíření můžou zahrnovat libovolné skripty, které se spouštějí v privilegovaném kontextu. proto je třeba zvážit, že kterýkoli Přispěvatel v prostředku Azure bude nepřímým správcem serveru, který není Azure.

Role registrace **počítače připojeného k Azure** je dostupná pro škálování na více počítačů a umožňuje číst nebo vytvářet nové servery s podporou ARC v Azure. Nedá se použít k odstranění serverů, které už jsou zaregistrované nebo spravují rozšíření. Jako osvědčený postup doporučujeme, abyste tuto roli přidělili jenom tomuto instančnímu objektu služby Azure Active Directory (Azure AD), který se používá k připojování počítačů ve velkém měřítku.

Uživatelé jako členové role **Správce prostředků počítače připojeného k Azure** můžou číst, měnit, reonboard a odstraňovat počítač. Tato role je navržená tak, aby podporovala správu serverů s podporou ARC, ale ne jiných prostředků v rámci skupiny prostředků nebo předplatného.

## <a name="agent-security-and-permissions"></a>Zabezpečení a oprávnění agenta

Pokud chcete spravovat agenta připojeného počítače Azure (azcmagent), musí být ve Windows váš uživatelský účet členem místní skupiny Administrators a v systému Linux musíte mít oprávnění root Access.

Agent připojeného počítače Azure se skládá ze tří služeb, které běží na vašem počítači.

* Služba Hybrid Instance Metadata Service (himds) zodpovídá za všechny základní funkce ARC. To zahrnuje odesílání prezenčních signálů do Azure, vystavení místní instance služby metadat pro jiné aplikace, které vám pomůžou získat informace o ID prostředku Azure daného počítače a načíst tokeny Azure AD k ověřování pro jiné služby Azure. Tato služba běží jako Neprivilegovaný účet virtuální služby ve Windows a jako **himds** uživatel na platformě Linux.

* Služba GCService (Host Configuration Service) zodpovídá za vyhodnocení Azure Policy v počítači.

* Služba rozšíření konfigurace hosta (ExtensionService) zodpovídá za instalaci, aktualizaci a odstranění rozšíření (agenti, skriptů nebo jiného softwaru) v počítači.

Služba konfigurace a rozšíření hosta běží jako místní systém ve Windows a jako kořenová složka v systému Linux.

## <a name="using-a-managed-identity-with-arc-enabled-servers"></a>Použití spravované identity se servery s podporou ARC

Ve výchozím nastavení se identita přiřazená Azure Active Directory systému, kterou používá ARC, dá použít jenom k aktualizaci stavu serveru s povoleným ARC v Azure. Například *Poslední zobrazený* stav prezenčního signálu. Případně můžete identitě přiřadit další role, pokud aplikace na serveru používá identitu přiřazenou systémem pro přístup k dalším službám Azure.

I když je k hybridnímu Instance Metadata Service přistupná nějaká aplikace spuštěná v počítači, můžou požádat o token Azure AD pro identitu přiřazenou systémem jenom autorizované aplikace. Při prvním pokusu o přístup k identifikátoru URI tokenu služba vygeneruje náhodně vygenerovaný kryptografický objekt BLOB v umístění v systému souborů, který mohou číst pouze důvěryhodní volající. Volající pak musí soubor přečíst (prokazující, že má příslušné oprávnění) a opakovat požadavek s obsahem souboru v autorizační hlavičce, aby úspěšně získal token Azure AD.

* Ve Windows musí být volající členem skupiny místních **správců** nebo **aplikací rozšíření hybridního agenta** pro čtení objektu BLOB.

* V systému Linux musí být volající členem skupiny **himds** pro čtení objektu BLOB.

## <a name="using-disk-encryption"></a>Použití šifrování disku

Agent připojeného počítače Azure používá ke komunikaci se službou Azure ověřování pomocí veřejného klíče. Po připojení serveru k Arc Azure se privátní klíč uloží na disk a použije se vždycky, když agent komunikuje s Azure. V případě odcizení lze privátní klíč použít na jiném serveru ke komunikaci se službou a fungovat jako původní server. To zahrnuje získání přístupu k identitě přiřazené systému a k prostředkům, ke kterým má identita přístup. Soubor privátního klíče je chráněný, aby mohl jenom přístup k účtu **himds** , aby ho bylo možné číst. Aby nedocházelo k offline útokům, důrazně doporučujeme použití úplného šifrování disku (například BitLocker, dm-crypt atd.) na svazku operačního systému vašeho serveru.

## <a name="next-steps"></a>Další kroky

Před vyhodnocením nebo povolením serverů s povoleným ARC (ve verzi Preview) na více hybridních počítačích si přečtěte téma [Přehled agenta připojení počítačů](agent-overview.md) , abyste pochopili požadavky, technické podrobnosti o agentovi a metody nasazení.
