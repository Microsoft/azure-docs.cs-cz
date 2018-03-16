---
title: "Odkazy na stránce nejsou k dispozici pro aplikaci Proxy aplikace | Microsoft Docs"
description: "Řešení potíží s poškozených odkazů na Proxy aplikace aplikací, které mají integrované s Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 17f2afb0aaf3b899784a504b77f33a1284f0a232
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Odkazy na stránce nejsou k dispozici pro aplikaci Proxy aplikace

Tento článek pomoci při řešení proto odkazy na aplikace Proxy aplikace služby Active Directory Azure nefungují správně.

## <a name="overview"></a>Přehled 
Po publikování aplikace Proxy aplikace, jenom odkazy, které fungují ve výchozím nastavení v aplikaci jsou odkazy na cíle obsažené v publikované kořenové adresy URL. Odkazy v rámci aplikace nefungují, interní adresa URL pro aplikaci pravděpodobně nezahrnuje všechny cíle odkazy v aplikaci.

**Proč to stává?** Pokud kliknutím na odkaz v aplikaci, Proxy aplikace se pokusí přeložit adresu URL jako buď interní adresu URL uvnitř stejné aplikaci, nebo jako adresu URL externě dostupný. Pokud na odkaz odkazuje na interní adresu URL, která není v rámci stejné aplikaci, se nepodporuje patří k oběma těmito oblastmi a způsobit chybu nebyl nalezen.

## <a name="ways-you-can-resolve-broken-links"></a>Způsoby, můžete vyřešit nefunkční odkazy

Existují tři způsoby, jak tento problém vyřešit. Volby níže jsou uvedeny v zvýšit složitost.

1.  Zajistěte, aby že interní adresa URL je kořenovou, která obsahuje všechny odkazy, které jsou relevantní pro aplikaci. To umožňuje všechny odkazy na možné přeložit jako obsah publikovat v rámci stejné aplikaci.

    Pokud změníte interní adresa URL, ale nechcete změnit cílová stránka pro uživatele, změňte adresu URL domovské stránky publikované dříve interní adresa URL. To lze provést přejdete do "Azure Active Directory" -&gt; registrace aplikace -&gt; vyberte aplikaci -&gt; vlastnosti. Na této kartě Vlastnosti zobrazí pole "Domovskou stránku URL", lze ji nastavit na požadovanou cílová stránka.

2.  Pokud vaše aplikace použít plně kvalifikované názvy domény (FQDN), použijte [vlastní domény](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) k publikování aplikací. Tato funkce umožňuje stejnou adresu URL, který se má použít jak interně a externě.

    Tato možnost zajistí odkazy v aplikaci externě přístupné prostřednictvím Proxy aplikace vzhledem k tomu, že na odkazy v aplikaci na interní adresy URL jsou také rozpoznány externě. Všimněte si, že všechny odkazy stále potřeba patří k publikované aplikaci. Však pomocí této možnosti odkazy nemusí patřit do stejné aplikaci a můžou patřit do více aplikací.

3.  Pokud žádná z těchto možností jsou vhodná, připojíte preview pro novou funkci, která provádějí překlad/přepisování adresy URL. Tato možnost, interní adresy URL nebo odkazy, které existují v těle HTML aplikací se přeložit, nebo "namapované", na publikované externí Proxy adresy URL aplikací. To funguje pouze pro odkazy HTML nebo šablon stylů CSS, a to není pomoct v případě odkaz na vaši je generována prostřednictvím JS. 

V důsledku toho důrazně doporučujeme používat [vlastní domény](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) řešení, pokud je to možné. Pokud chcete pro připojení ve verzi preview, e-mailu <aadapfeedback@microsoft.com> s applicationId(s).

## <a name="next-steps"></a>Další postup
[Práce s existující místní proxy servery](application-proxy-working-with-proxy-servers.md)

