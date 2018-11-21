---
title: Jak naplánovat vaši implementaci připojení k Azure Active Directory (Azure AD) | Dokumentace Microsoftu
description: Vysvětluje kroky, které jsou nutné k implementaci služby Azure AD připojené zařízení ve vašem prostředí.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: fdc8b5db9316e463f8ec46ca5e235ea53cf44265
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275968"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Postupy: Naplánování vaší implementace připojení k Azure AD


Připojení k Azure AD umožňuje připojit přímo k Azure AD bez nutnosti připojení k místní službě Active Directory a zajistit přitom ochranu uživatelům zajistit produktivitu a zabezpečení zařízení.  

Tento článek předpokládá, že jste seznámení se s Azure AD a [stavy zařízení ve službě Azure AD](overview.md). 

 

## <a name="review-your-scenarios"></a>Zkontrolujte vaše scénáře 

Při připojení ke službě Azure AD je možné využít pro většinu scénářů v prostředí organizace, může být určité případy použití, kde můžete chtít místo toho nasazení připojení k hybridní službě Azure AD. Připojení k Azure AD je připravená pro podnikové nasazení ve velkém měřítku nebo oboru. 

 
Vezměte v úvahu Azure AD join podle následujících kritérií pro vaši organizaci:  

- Plán nebo po mít většinou zařízení Windows ve Windows 10. 

- Máte v úmyslu přesunout do cloudu řízené nasazení a správu zařízení Windows. 

- Mají omezenou na místní infrastrukturu nebo chcete snížit nároky na místní. 

- Není nutné silné závislosti na zásadách skupiny pro správu zařízení. 

- Není nutné starších aplikací, které využívají ověřování počítače služby Active Directory. 

- Všechny nebo vybrané uživatele ve vaší organizaci nemusí být v podnikové síti pro přístup k potřeby aplikací a prostředků.  

 

Kontrola scénáře na základě následujících posouzení  

 

## <a name="assess-infrastructure"></a>Vyhodnocení infrastruktury  

 

### <a name="users"></a>Uživatelé 

Pokud vaši uživatelé jsou vytvořené v místní službě Active Directory, musí být synchronizovány do služby Azure AD prostřednictvím služby Azure AD Connect. Další informace o synchronizaci uživatelů najdete v tématu [co je Azure AD Connect?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity#what-is-azure-ad-connect) 

Pokud vaši uživatelé jsou vytvořené přímo v Azure AD, není zapotřebí žádné další nastavení 

 

Alternativní přihlašovací ID nejsou podporované na zařízeních připojených k Azure AD. Uživatelé by měli mít platný hlavní název uživatele ve službě Azure AD.  

 

### <a name="identity-infrastructure"></a>Infrastruktura identit 

Připojení k Azure AD funguje s federované i spravované prostředí.  

#### <a name="managed-environment"></a>Spravované prostředí 

Spravované prostředí je možné nasadit prostřednictvím synchronizace hodnot Hash hesel nebo předávací ověřování pomocí bezproblémového jednotného přihlašování. <read more here> 

#### <a name="federated-environment"></a>Federovaném prostředí  

Federovaném prostředí by měl mít zprostředkovatele identity, která podporuje WS-Trust a WS-Fed protokoly. WS-Fed se vyžaduje k připojení zařízení k Azure AD, WS-Trust se vyžaduje pro přihlášení k zařízení připojené k doméně Azure AD. Ano Pokud váš zprostředkovatel identity nepodporuje je připojení ke službě Azure AD nefunguje. 


#### <a name="smartcards-and-certificate-based-authentication"></a>Čipové karty a ověřování na základě certifikátu 

 

Nejsou v současnosti čipové karty a ověřování pomocí certifikátů podporované ve službě Azure AD, tak nebude fungovat na zařízeních připojených k Azure AD 

 

### <a name="devices"></a>Zařízení 

 

#### <a name="supported-devices"></a>Podporovaná zařízení 

Připojení k Azure AD je výhradně pro zařízení s Windows 10. To neplatí pro předchozí verze Windows nebo jinými operačními systémy. Pokud máte Windows 7/8.1, je nutné upgradovat na Windows 10 k nasazení připojení k Azure AD 

 

Doporučení: Vždy používejte nejnovější verzi Windows 10 pro aktualizace funkcí 

 

### <a name="applications--other-resources"></a>Aplikace a další zdroje informací 

Doporučujeme že migrovat aplikace z místního cloudu pro lepší výkon a řízení přístupu. Nicméně připojená k Azure AD zařízení můžete bez problémů poskytují přístup k místním i cloudovým aplikacím. Další informace najdete v tématu [jak jednotné přihlašování funguje s místními prostředky v Azure AD zařízení připojená k](azuread-join-sso.md).  


Tady jsou důležité informace týkající se jiný typ aplikace a prostředky 

 

- **Aplikace založené na cloudu:** Pokud aplikace je přidána do Galerie aplikací Azure AD, uživatelé získat jednotné přihlašování prostřednictvím služby Azure AD zařízení připojená k. Není nutná žádná další konfigurace 

 

- **Místní webové aplikace:** Pokud jsou vaše aplikace vlastní vytvořené a hostované místně, musíte je přidat do důvěryhodných lokalit prohlížeče. To vám umožní ověření integrované Windows fungovat a poskytovat jednotné přihlašování bez výzvy pro uživatele. Pokud používáte službu AD FS, přečtěte si téma [ověřte a Správa jednotného přihlašování se službou AD FS](https://docs.microsoft.com/en-us/previous-versions/azure/azure-services/jj151809(v%3dazure.100)) Další informace. Doporučení: Zvažte hostování v cloudu (například Azure) a integrace s Azure AD pro vyšší výkon. 

- **Místní aplikace spoléhat na starších verzí protokolů:** get uživatelům jednotné přihlašování z Azure AD zařízení připojená k Pokud má zařízení dohlednost služby k řadiči domény. Doporučení: Nasazení služby proxy aplikace Azure AD povolit zabezpečený přístup k těmto aplikacím. Další informace najdete v tématu [Proč je lepší řešení Proxy aplikací?](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#why-is-application-proxy-a-better-solution) 

- **Místní sdílené síťové složky:** get uživatelům jednotné přihlašování ke sdílené složce jejich ze služby Azure AD zařízení připojená k při jejich zařízení může připojit k řadiči domény. Další informace najdete v tématu [jak jednotné přihlašování funguje s místními prostředky v Azure AD zařízení připojená k](azuread-join-sso.md).

 

- **Tiskárny:** zařízení připojená k hybridní nasazení cloud print pro zjišťování tiskáren ze služby Azure AD. Můžete také uživatelům použít také cestu UNC k tiskárnám přímo je přidat. V cloudovém prostředí pouze nelze automaticky zjišťovat tiskárny. 

 

- **Místní aplikace spoléhat na ověřování počítače:** tyto aplikace nejsou podporované na zařízeních připojených k Azure AD. Doporučení: Zvažte vyřazení těchto aplikací a přesunutí do svých moderních alternativy.  

 

### <a name="device-management"></a>Správa zařízení  

 

Správa zařízení pro zařízení připojená k Azure AD je primárně prostřednictvím platformy EMM (například Intune) a služby MDM CSP. Windows 10 obsahuje integrovaného agenta MDM, který funguje u všech kompatibilní řešení EMM.  

 

Zásady skupiny se nepodporují na zařízeních připojených k Azure AD, protože nejste připojení ke službě Active Directory.  

 

Vyhodnocení parity zásady MDM pomocí zásad skupiny [analýza nástroj pro migraci MDM [MMAT](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/THR3002R). Zkontrolujte podporované a nepodporované zásady určit použitelnost pomocí řešení EMM místo zásad skupiny. Nepodporovaná zásady vezměte v úvahu:  

- Nepodporovaná zásady jsou nezbytné pro uživatele nebo zařízení Azure AD join se nasazuje do? 

- Nepodporovaná zásady platí v cloudu řízené nasazení? 

 

Pokud vaše řešení EMM není k dispozici prostřednictvím Galerie aplikací Azure AD, můžete ho vysvětleného v [integrace služby Azure Active Directory s MDM](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm).

 

Prostřednictvím spolusprávy je možné spravovat některé aspekty zařízení, když zásady jsou poskytovány pomocí vaší platformě EMM SCCM. Microsoft Intune povolí spoluspráva pomocí SCCM. Další informace najdete v tématu [zařízení s Windows 10 pro spolusprávu](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Pokud používáte o produkt poskytovaný EMM než Intune, zkontrolujte u svého poskytovatele EMM na příslušné spolusprávy scénáře.  

 

Existují dva různé způsoby správy zařízení připojených k Azure AD: 

 

- **Jen pro MDM** – zařízení se spravují výhradně poskytovatel EMM jako je Intune. Všechny zásady se dodávají jako součást procesu registrace MDM. Pro zákazníky Azure AD Premium nebo EMS je automatické registrace MDM v rámci připojení ke službě Azure AD. 

- **Společná správa** – zařízení se spravuje současně pomocí poskytovatel EMM a SCCM. V takovém případě je SCCM agent nainstalovaný na zařízení spravovaná pomocí MDM spravovat některé aspekty. 

Doporučení: Zvažte MDM jen pro zařízení připojená k správy pro službu Azure AD.  

 

## <a name="configuration"></a>Konfigurace 

 

Připojení k Azure AD můžete nakonfigurovat na portálu Azure AD podle některých specifických nastavení. Přejděte na `Devices -> Device settings` a nakonfigurujte následující možnosti:   

- Uživatelé můžou připojovat zařízení do služby Azure AD: nastavte na "Vše" nebo "Vybraný" v závislosti na rozsah nasazení.  

- Zařízení připojená k další místní správci na Azure AD: nastavte na "Vybraný" a vybrat uživatele, kterým chcete přidat do skupiny místních správců na všech Azure AD zařízení připojená k nasazené ve vaší organizaci. Další informace najdete v tématu [zařízení připojená k tom, jak spravovat místní skupiny administrators na Azure AD](assign-local-admin.md). 

- Vyžadovat vícefaktorové ověřování pro připojení zařízení: nastavte na "Ano", pokud vyžadujete, aby uživatelům provádět vícefaktorové ověřování při připojení zařízení k Azure AD. Pro uživatele, který připojí zařízení k Azure AD pomocí vícefaktorového ověřování je označeno jako zařízení 2nd factor. 

Pokud chcete povolit stav roamingu do služby Azure AD tak, aby se zařízení můžou synchronizovat nastavení, najdete v článku [co enterprise stav roamingu?](enterprise-state-roaming-overview.md). Doporučujeme povolit toto nastavení i pro Azure hybridních zařízení připojených k AD 

### <a name="deployment-options"></a>Možnosti nasazení 

 

Připojení k Azure AD se dá nasadit pomocí tří různých přístupů:  

- **Samoobslužná služba v OOBE/nastavení** – v režimu samoobslužného uživatele go prostřednictvím připojení k Azure AD buď během Windows mimo prostředí (OOBE) nebo z Windows nastavení procesu.  

- **Windows Autopilot** – Windows Autopilot umožňuje před konfigurací zařízení pro plynulost v OOBE k provedení připojení ke službě Azure AD.   

- **Hromadná registrace** -hromadný zápis umožňuje na základě správce Azure AD join pomocí hromadného zřizování nástroje pro konfiguraci zařízení.  


Tady je porovnání těchto tří metod 

 
||Nastavení samoobslužných služeb|Windows Autopilot|Hromadná registrace|
|---|---|---|---|
|Vyžadovat interakci uživatele k nastavení|Ano|Ano|Ne|
|Vyžadovat IT úsilí|Ne|Ano|Ano|
|Použitelné toky|Nastavení & OOBE|Pouze OOBE|Pouze OOBE|
|Práva místního správce na primárním uživatelem|Ano, ve výchozím nastavení|Konfigurovatelné|Ne|
|Nepožadujete podporu zařízení OEM|Ne|Ano|Ne|
|Podporované verze|verze 1511 +|1709 +|1703 +|
 
Zvolte postup nasazení nebo přístupy revize výše uvedené tabulce a kontrolou následující důležité informace pro přijetí kterýkoliv přístup:  

- Jsou vaši uživatelé Odborný koumák projděte si instalační program sami? 

    - Samoobslužné služby mohou nejvhodnější pro tyto uživatele. Vezměte v úvahu Windows Autopilotu a zlepšit uživatelské prostředí.  

- Uživatelé jsou vzdálené nebo v rámci podnikové místní? 

    - Samoobslužné nebo Autopilot práce pro vzdálené uživatele pro nastavení přehledného nejvhodnější. 
 
- Upřednostňujete řízené uživatelem nebo konfiguraci služby spravované správce? 

    - Hromadná registrace fungovala lépe pro správce řízené nasazení si nejdřív vytvořit zařízení předání uživatelům.     

- Zakoupit zařízení od výrobců OEM 1-2, nebo máte široké distribuci OEM zařízení?  

    - Pokud Nákup z omezené výrobce OEM, kteří podporují také Autopilot, vám může přinést užší integraci s Autopilot. 
 

 


## <a name="next-steps"></a>Další postup

- [Správa zařízení](overview.md)

