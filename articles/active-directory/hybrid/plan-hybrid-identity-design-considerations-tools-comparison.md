---
title: 'Hybridní identita: Porovnání nástrojů pro integraci adresáře | Dokumentace Microsoftu'
description: Tato stránka nabízí komplexní tabulku porovnávající různé nástroje integrace adresáře, které lze použít pro integraci adresáře.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 26c00ba1dc6a4d597db8fe067d28d687f50412b3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194486"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Hybridní identita: Porovnání nástrojů pro integraci adresáře
V průběhu let se nástroje pro integraci adresáře rozšířily a vyvinuly.  Tento dokument vám poskytne ucelený přehled těchto nástrojů a porovnání funkcí, které jsou v nich dostupné.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Azure AD Connect zahrnuje komponenty a funkce dříve vydávané jako Dirsync a AAD Sync. Tyto nástroje se již nebudou vydávat jednotlivě a všechna budoucí vylepšení budou zahrnuta do aktualizací služby Azure AD Connect, abyste vždycky věděli, kde sehnat ty nejnovější funkce.
> 
> DirSync a Azure AD Sync jsou nyní zastaralé. Další informace najdete [tady](reference-connect-dirsync-deprecated.md).
> 
> 

U jednotlivých tabulek použijte následující klíč.

● = Nyní dostupné  
FR = budoucí verze  
PP = Public Preview  

## <a name="on-premises-to-cloud-synchronization"></a>Synchronizace z místního prostředí do cloudu
| Funkce | Azure Active Directory Connect | Synchronizační služba služby Azure Active Directory (AAD Sync) – NADÁLE SE NEPODPORUJE | Nástroj pro synchronizaci adresáře služeb Azure Active Directory (DirSync) – NADÁLE SE NEPODPORUJE | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Připojení k jedné místní doménové struktuře AD |● |● |● |● |● |
| Připojení k několika místním doménovým strukturám AD |● |● | |● |● |
| Připojení k několika místním Exchange Orgs |● | | | | |
| Připojení k jednomu místnímu adresáři LDAP |●* | | |● |● | 
| Připojení k několika místním adresářům LDAP |●*  | | |● |● | 
| Připojení k místním adresářům AD a LDAP |●* | | |● |● | 
| Připojení k vlastním systémům (tj. SQL, Oracle, MySQL atd.) |FR | | |● |● |
| Synchronizace atributů definovaných zákazníkem (rozšíření adresáře) |● | | | | |
| Připojení k místním HR (tj, SAP, Oracle eBusiness, PeopleSoft) |FR | | |● |● |
| Podporuje pravidla synchronizace FIM a konektory pro zřizování do místních systémů. | | | |● |● |

 
&#42; Aktuálně jsou dostupné dvě podporované možnosti.  Jsou to tyto: 

   1. Můžete použít obecný konektor LDAP a povolit ho mimo Azure AD Connect.  Tato operace je složitá a vyžaduje partnera pro onboarding a smlouvu Premier Support.  Tato možnost umožňuje pracovat s jednotlivými adresáři LDAP i s několika adresáři současně. 

   2. Můžete vyvíjet vlastní řešení pro přesun objektů ze LDAP do služby Active Directory.  Potom tyto objekty synchronizujete s využitím Azure AD Connect.  Jako možné řešení pro přesun objektů se dá využít MIM nebo FIM. 

## <a name="cloud-to-on-premises-synchronization"></a>Synchronizace z cloudu do místního prostředí
| Funkce | Azure Active Directory Connect | Synchronizační služba služby Azure Active Directory – NADÁLE SE NEPODPORUJE  | Nástroj pro synchronizaci adresáře služeb Azure Active Directory (DirSync) – NADÁLE SE NEPODPORUJE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Zpětný zápis zařízení |● | |● | | |
| Zpětný zápis atributů (pro hybridního nasazení Exchange) |● |● |● |● |● |
| Zpětný zápis skupin objektů |● | | | | |
| Zpětný zápis hesel (ze samoobslužného resetování hesla (SSPR) a změny hesla) |● |● | | | |

## <a name="authentication-feature-support"></a>Podpora funkce ověřování
| Funkce | Azure Active Directory Connect | Synchronizační služba služby Azure Active Directory – NADÁLE SE NEPODPORUJE  | Nástroj pro synchronizaci adresáře služeb Azure Active Directory (DirSync) – NADÁLE SE NEPODPORUJE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Synchronizace hodnot hash hesel pro jednu místní doménovou strukturu AD |●|●|● | | |
| Synchronizace hodnot hash hesel pro několik místních doménových struktur AD |●|● | | | |
| Předávací ověřování pro jednu místní doménovou strukturu AD |●| | | | |
| Jednotné přihlašování s federací |● |● |● |● |● |
| Bezproblémové jednotné přihlašování|● |||||
| Zpětný zápis hesel (ze SSPR a změny hesla) |● |● | | | |

## <a name="set-up-and-installation"></a>Nastavení a instalace
| Funkce | Azure Active Directory Connect | Synchronizační služba služby Azure Active Directory – NADÁLE SE NEPODPORUJE  | Nástroj pro synchronizaci adresáře služeb Azure Active Directory (DirSync) – NADÁLE SE NEPODPORUJE  | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| Podporuje instalaci na řadič domény |● |● |● | |
| Podporuje instalaci pomocí SQL Express |● |● |● | |
| Snadný upgrade z nástroje DirSync |● | | | |
| Lokalizace správce UX pro jazyky Windows Serveru |● |● |● | |
| Lokalizace koncového uživatele UX pro jazyky Windows Serveru | | | |● |
| Podpora pro Windows Server 2008 a Windows Server 2008 R2 |● pro synchronizaci, ne pro federaci |● |● |● |
| Podpora pro Windows Server 2012 a Windows Server 2012 R2 |● |● |● |● |

## <a name="filtering-and-configuration"></a>Filtrování a konfigurace
| Funkce | Azure Active Directory Connect | Synchronizační služba služby Azure Active Directory – NADÁLE SE NEPODPORUJE  | Nástroj pro synchronizaci adresáře služeb Azure Active Directory (DirSync) – NADÁLE SE NEPODPORUJE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Filtrování v rámci domén a organizačních jednotek |● |● |● |● |● |
| Filtrování v rámci hodnot atributů objektů |● |● |● |● |● |
| Povolení minimální sady atributů, které mají být synchronizovány (MinSync) |● |● | | | |
| Povolení použití jiných šablon služeb pro toky atributů |● |● | | | |
| Povolení odebrání atributů z toku ze služby AD do služby Azure AD |● |● | | | |
| Povolení upřesňujících úprav pro toky atributů |● |● | |● |● |

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

