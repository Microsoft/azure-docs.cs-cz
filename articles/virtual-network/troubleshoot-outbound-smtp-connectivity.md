---
title: Řešení potíží s odchozím připojením SMTP v Azure | Microsoft Docs
description: Přečtěte si doporučenou metodu pro posílání e-mailů a řešení potíží s odchozím připojením SMTP v Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: 518b8e33ac9adfdd1aa121e0cb8d1558545980e7
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054850"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Řešení potíží s odchozím připojením SMTP v Azure

Od 15. listopadu 2017 se odchozí e-mailové zprávy, které se odesílají přímo do externích domén (například outlook.com a gmail.com) z virtuálního počítače, zpřístupňují jenom pro určité typy předplatného v Azure. Odchozí připojení SMTP, která používají port TCP 25, byla zablokována. (Port 25 se používá hlavně pro neověřené doručování e-mailů.)

Tato změna chování se týká jenom předplatných a nasazení, která se vytvořila po 15. listopadu 2017.

## <a name="recommended-method-of-sending-email"></a>Doporučený způsob odesílání e-mailů

Pro posílání e-mailů z virtuálních počítačů Azure nebo z Azure App Service doporučujeme použít ověřené služby SMTP relay. (Tyto přenosové služby se obvykle připojují přes port TCP 587, ale podporují jiné porty.) Tyto služby se používají k údržbě IP nebo reputace domény, aby se minimalizovala možnost, že poskytovatelé e-mailů od jiných výrobců odmítnou zprávy. [SendGrid](https://sendgrid.com/partners/azure/) je taková přenosová služba SMTP, ale existují další. Je možné, že máte také zabezpečenou službu SMTP relay, která běží místně, kterou můžete použít.

Použití těchto služeb doručování e-mailů není v Azure omezené bez ohledu na typ předplatného.

## <a name="enterprise-agreement"></a>Smlouva Enterprise

U smlouva Enterprise uživatelů Azure se nemění technická schopnost posílat e-maily bez použití ověřeného přenosu. Noví i stávající smlouva Enterprise uživatelé můžou zkusit odchozí e-mailové doručování z virtuálních počítačů Azure přímo externím poskytovatelům e-mailu bez jakýchkoli omezení platformy Azure. Není nijak zaručeno, že poskytovatelé e-mailů budou přijímat příchozí e-maily od kteréhokoli daného uživatele. Platforma Azure ale nebude blokovat pokusy o doručení pro virtuální počítače v rámci předplatných smlouva Enterprise. Budete muset pracovat přímo s poskytovateli e-mailu, aby se opravily problémy při doručování zpráv nebo filtrování SPAMu, které zahrnují konkrétní poskytovatele.

## <a name="pay-as-you-go"></a>Průběžné platby

Pokud jste se zaregistrovali před 15. listopadu 2017 pro předplatné s průběžnými platbami, nebudete mít žádnou změnu ve vaší technické schopnosti vyzkoušet odchozí e-mailové doručení. Pořád budete moct vyzkoušet odchozí e-mailové doručování z virtuálních počítačů Azure v rámci těchto předplatných přímo na externí poskytovatele e-mailů bez jakýchkoli omezení platformy Azure. Opět není nijak zaručeno, že poskytovatelé e-mailů budou přijímat příchozí e-maily od kteréhokoli daného uživatele. Uživatelé budou muset pracovat přímo s poskytovateli e-mailu, aby opravili problémy s doručováním zpráv nebo filtrováním SPAMu, které zahrnují konkrétní poskytovatele.

V případě předplatných s průběžnými platbami, které byly vytvořeny po 15. listopadu 2017, budou k dispozici technická omezení, která blokují e-mail, který se odesílá přímo z virtuálních počítačů v rámci předplatných. Pokud chcete mít možnost odesílat e-maily z virtuálních počítačů Azure přímo externím poskytovatelům e-mailu (bez použití ověřeného přenosu SMTP) a máte účet v dobrém umístění s historií plateb, můžete požádat o odebrání omezení. To můžete udělat **v okně** **diagnostikovat a řešit** pro prostředek Azure Virtual Network v Azure Portal. Pokud je vaše žádost přijata, vaše předplatné se povolí nebo obdržíte pokyny k dalším krokům. 

Po vyloučení předplatného s průběžnými platbami a při zastavení a restartování virtuálních počítačů v Azure Portal jsou všechny virtuální počítače v tomto předplatném předány dál. Výjimka platí jenom pro vyžádané předplatné a jenom pro přenosy virtuálních počítačů, které jsou směrované přímo na Internet.

> [!NOTE]
> Společnost Microsoft si vyhrazuje právo tyto výjimky odvolat, pokud se zjistí, že došlo k porušení podmínek služby.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure Pass, Systém Azure v rámci licenčního programu Open, vzdělávání, Azure pro studenty, Visual Studio a bezplatná zkušební verze

Pokud jste po 15. listopadu 2017 vytvořili jeden z následujících typů předplatného, budete mít technická omezení, která blokují e-mail odeslaný z virtuálních počítačů v rámci předplatného přímo poskytovatelům e-mailu:
- MSDN
- Azure Pass
- Azure v rámci licenčního programu Open
- Vzdělávání
- Azure for Students
- Bezplatná zkušební verze
- Jakékoli předplatné sady Visual Studio  

Omezení jsou zavedena pro zabránění zneužití. Žádosti o odebrání těchto omezení nebudou uděleny.

Pokud používáte tyto typy předplatného, doporučujeme vám používat předávací služby SMTP, jak je uvedeno výše v tomto článku, nebo změnit typ předplatného.

## <a name="cloud-solution-provider"></a>Program Cloud Solution Provider

Pokud používáte prostředky Azure prostřednictvím poskytovatele Cloud Solution Provider, můžete vytvořit žádost o odebrání omezení v části **připojení** v podokně **Diagnostika a řešení** pro prostředek virtuální sítě v Azure Portal. Pokud je vaše žádost přijata, vaše předplatné se povolí nebo obdržíte pokyny k dalším krokům.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network, BizSpark Plus nebo Azure Sponsorship

Pro odběry následujících typů, které byly vytvořeny po 15. listopadu 2017, budou k dispozici technická omezení, která blokují e-mail, který je odesílán přímo z virtuálních počítačů v rámci předplatných:

- Microsoft Partner Network (MPN)
- BizSpark Plus
- Azure Sponsorship

Pokud chcete mít možnost odesílat e-maily z virtuálních počítačů Azure přímo externím poskytovatelům e-mailu (bez použití ověřeného přenosu SMTP), můžete vytvořit žádost otevřením případu podpory pomocí následujícího typu problému: **technické**  >  **Virtual Network**  >  **připojení**  >  **nemůže odesílat e-maily (SMTP/port 25)**. Nezapomeňte přidat podrobnosti o tom, proč musí vaše nasazení odeslat e-mailem přímo poskytovatelům pošty místo použití ověřeného přenosu. Žádosti budou přezkoumány a schváleny na uvážení společnosti Microsoft. Žádosti budou uděleny až po dokončení dalších kontrol podvodu. 

Po vyloučení předplatného a po zastavení a restartu virtuálních počítačů v Azure Portal dojde k vyloučení všech virtuálních počítačů v tomto předplatném. Výjimka platí jenom pro vyžádané předplatné a jenom pro přenosy virtuálních počítačů, které jsou směrované přímo na Internet.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , abyste mohli rychle vyřešit problém. Použijte tento typ problému: **Technická**  >  **Virtual Network**  >  **konektivita**  >  **nemůže odesílat e-maily (SMTP/port 25)**.
