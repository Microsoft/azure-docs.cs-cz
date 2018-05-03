Virtuální počítače v Azure můžete vytvořit pomocí Průzkumníka serveru v sadě Visual Studio.

## <a name="create-an-azure-virtual-machine-in-server-explorer"></a>Vytvoření virtuálního počítače Azure v Průzkumníku serveru
Můžete si sice vytvořit virtuální počítač v [portálu pro správu Azure](http://go.microsoft.com/fwlink/?LinkID=253103), můžete také vytvořit virtuální počítač v Azure pomocí příkazů v Průzkumníku serveru. Virtuální počítače můžete použít například k poskytování front-end za běžných Vyrovnávání zatížení sítě veřejný koncový bod.

### <a name="to-create-a-new-virtual-machine"></a>K vytvoření nového virtuálního počítače
1. V Průzkumníku serveru, otevřete **Azure** uzel a klikněte na tlačítko **virtuální počítače**.
2. V místní nabídce klikněte na tlačítko **vytvořit virtuální počítač**.
   
    **Vytvořit nový virtuální počítač** zobrazí se průvodce.
   
    ![Příkaz Vytvořit virtuální počítač](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718342.png)
3. Na **zvolte předplatné** vyberte předplatné pro použití při vytváření virtuálního počítače a pak klikněte na tlačítko **Další**.
   
    Pokud nejste přihlášení do Azure, klikněte na tlačítko **přihlásit** k přihlášení. Potom vyberte předplatné Azure v rozevíracím seznamu, pokud ještě nebyla vybrána.
4. Na **vyberte bitovou kopii virtuálního počítače** vyberte typ bitové kopie v **typem bitové kopie** rozevírací seznam pole a pak vyberte bitové kopie virtuálních počítačů v **název bitové kopie** pole se seznamem . Když jste hotovi, klikněte na tlačítko **Další**.
   
    ![Vybrat stránku bitové kopie virtuálního počítače](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744137.png)
   
    Můžete použít následující typy obrázků.
   
   * **Veřejné obrázky** uvádí bitové kopie virtuálních počítačů, operačních systémů a server software, například Windows Server a SQL Server.
   * **Imagím MSDN** uvádí bitové kopie virtuálních počítačů předplatitelům služby MSDN, jako je například Visual Studio a Microsoft Dynamics dostupného softwaru.
   * **Obrázky privátní** seznamy specializuje a zobecněn bitové kopie virtuálních počítačů, které jste vytvořili.
     
     Další informace o specializované a zobecněný virtuální počítače najdete v tématu [Image virtuálního počítače](https://azure.microsoft.com/blog/2014/04/14/vm-image-blog-post/). V tématu [jak zachytit virtuální počítač Windows pro použití jako šablona](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) pro informace o tom, jak zapnout virtuální počítač do šablony, která vám pomůže rychle vytvořit nový předem nakonfigurované virtuální počítače.
     
     Můžete kliknout na název bitové kopie virtuálního počítače zobrazíte informace o bitovou kopii na pravé straně stránky.
     
     > [!NOTE]
     > Nelze přidat Image virtuálního počítače do **veřejné obrázky** nebo **Imagím MSDN** uvádí, protože jsou jen pro čtení. Všechny virtuální počítače, které vytvoříte, se přidají do **privátní image** seznamu.
     > 
     > 
     
     Pokud jste webu MSDN odběratele se Visual Studio na úrovni předplatného, můžete vytvořit předem připravené Azure virtuální počítač, který obsahuje sady Visual Studio, jakož i několik dalších bitové kopie. Další informace najdete v tématu [vytvoření virtuálního počítače v sadě Visual Studio pomocí bitové kopie Visual Studio 2013 Galerie obrázek pro předplatitele MSDN](http://visualstudio2013msdngalleryimage.azurewebsites.net) a [předplatné MSDN](https://www.visualstudio.com/products/msdn-subscriptions-vs). |
5. Na **základní nastavení virtuálního počítače** stránky, zadejte název počítače a poté přidejte specifikace pro virtuální počítač, včetně velikosti a uživatelské jméno a heslo. Když jste hotovi, klikněte na tlačítko **Další**.
   
    Nové jméno a heslo budete používat pro přihlášení k počítači pomocí vzdálené plochy, takže je vhodné k zápisu v případě, že byste zapomněli. Po vytvoření virtuálního počítače Azure v sadě Visual Studio, můžete změnit jeho velikost a další nastavení v [portálu pro správu Azure](http://go.microsoft.com/fwlink/?LinkID=253103).
   
   > [!NOTE]
   > Pokud si zvolíte větší velikosti pro virtuální počítač, další poplatky. V tématu [podrobnosti o cenách virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/) Další informace.
   > 
   > 
6. Virtuální počítače vytvořené v sadě Visual Studio vyžadují cloudové služby. Na **nastavení cloudové služby** vyberte cloudovou službu pro virtuální počítač nebo klikněte na tlačítko **< vytvořit novou... >** v rozevíracím seznamu, pokud ještě nemáte cloudové služby nebo chcete použít novou. Účet úložiště je také nutný, takže zvolte účet úložiště (nebo vytvořit nový účet úložiště) v **účet úložiště** rozevíracím seznamu. V tématu [Úvod do Microsoft Azure Storage](../articles/storage/common/storage-introduction.md) Další informace.
7. Pokud chcete určit virtuální sítě (což je volitelný), vyberte ho v rozevírací seznamy seznamu virtuální sítě a podsítě.
   
    Virtuální počítače, které jsou členy skupiny dostupnosti se nasadí do domén různých selhání. V tématu [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) Další informace.
8. Pokud chcete virtuální počítač patří do skupiny dostupnosti (volitelné), vyberte **zadejte nastavení dostupnosti** zaškrtněte políčko a potom vyberte v rozevíracím seznamu sadu dostupnosti. Až budete hotoví, vyberte **Další** tlačítko.
   
    Přidání virtuálního počítače do skupiny dostupnosti pomáhá vaší aplikace zůstanou k dispozici při selhání sítě, selhání hardwaru místního disku a žádné plánované výpadky. Budete muset použít [portálu pro správu Azure](http://go.microsoft.com/fwlink/?LinkID=253103) k vytvoření virtuální sítě, podsítě a dostupnost sady. V tématu [Správa dostupnosti virtuálních počítačů](https://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/) Další informace.
9. Na **koncové body** zadejte veřejné koncové body, které chcete mít k dispozici pro uživatele tohoto virtuálního počítače. Například je možné povolit HTTP (Port 80) kromě vzdálené plochy a prostředí PowerShell koncové body, které jsou ve výchozím nastavení povolené. Chcete-li přidat koncový bod, zvolte jeden v **název portu** rozevírací seznam pole a zvolte **přidat** tlačítko. Odebrat koncový bod, zvolte červený **X** vedle názvu v seznamu koncových bodů.
   
    ![Stránku koncové body v Průvodci vytvořením virtuálního počítače.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718351.png)
   
    Koncové body, které jsou k dispozici závisí na cloudové služby, které jste vybrali pro virtuální počítač. V tématu [koncové body služby Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) Další informace.
   
   > [!NOTE]
   > Povolení veřejné koncové body zpřístupní services na virtuální počítač k Internetu. Je nutné nainstalovat a správně nakonfigurujte koncové body a služby ve vašem virtuálním počítači, například seznamy nastavení řízení přístupu (ACL) pro koncové body. V tématu [jak nastavit koncové body k virtuálnímu počítači](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) Další informace.
   > 
   > 
10. Po dokončení konfigurace nastavení virtuálního počítače, vyberte **vytvořit** tlačítko pro vytvoření virtuálního počítače.
    
     Jak Azure vytvoří virtuální počítač, **protokol činnosti Azure** zobrazuje průběh operace vytvoření virtuálního počítače.
    
     ![Virtuální počítač protokol aktivit - probíhá.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744138.png)
    
     Chcete-li zobrazit pouze informace o virtuálním počítači, zvolte **virtuální počítače** ve **protokol činnosti Azure**.
    
     ![Protokol aktivit virtuálního počítače – byla dokončena.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744139.png)
    
     Pokud po úspěšném dokončení operace nového virtuálního počítače se objeví pod uzlem **virtuální počítače** uzlu v Průzkumníku serveru. Může přihlásit k ho kliknutím **připojit pomocí vzdálené plochy** zástupce.
    
     ![Virtuální počítač, které jsou uvedeny v Průzkumníku serveru.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744140.png)

## <a name="manage-your-virtual-machines"></a>Správa virtuálních počítačů
Na stránce konfigurace virtuálního počítače, kromě vypíná, připojení, aktualizace a přidání kontrolních bodů do vybraného virtuálního počítače můžete také zobrazit nebo změnit nastavení pro virtuální počítač. Můžete:

* Změníte velikost virtuálního počítače.
* Vyberte skupinu dostupnosti pro použití s virtuálním počítačem.
* Přidat, odebrat nebo změnit nastavení pro veřejné koncové body.
* Přidat, odebrat nebo nakonfigurovat rozšíření virtuálního počítače.
* Zobrazit informace o discích spojené s virtuálním počítačem.

### <a name="view-or-change-virtual-machine-settings"></a>Zobrazit nebo změnit nastavení virtuálního počítače
1. V Průzkumníku serveru, vyberte virtuální počítač v **virtuální počítače Azure** uzlu.
2. V místní nabídce vyberte **konfigurace** zobrazíte na stránce konfigurace virtuálního počítače.
   
    ![Stránka Konfigurace virtuálního počítače Azure](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744141.png)
3. Zobrazit informace o virtuálním počítači nebo ho změnit.

### <a name="save-or-restore-the-status-of-your-virtual-machine"></a>Uložit nebo obnovit stav virtuálního počítače
Jak nakonfigurovat virtuální počítač a na něj nainstalovat software, je dobré pravidelně rozdělanou práci uložit tak, že vytvoříte kontrolní body virtuálních počítačů. Kontrolní bod je snímek, nebo bitové kopie aktuálního stavu virtuálního počítače. Pokud se něco pokazí s virtuálním počítačem, nebo chcete změnit konfiguraci virtuálního počítače, můžete ušetřit čas obnovení do předchozího stavu kontrolního bodu spíše než přes od začátku.

### <a name="to-create-a-virtual-machine-checkpoint"></a>Chcete-li vytvořit kontrolní bod virtuálního počítače
1. V Průzkumníku serveru, vyberte virtuální počítač v **virtuální počítače Azure** uzlu.
2. V místní nabídce vyberte **konfigurace** zobrazíte na stránce konfigurace virtuálního počítače.
3. Na stránce konfigurace, zvolte **zachytit bitovou kopii** tlačítko.
   
    ![Tlačítko zachycení stránku konfigurace služby Azure](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744142.png)
   
    **Zachytit virtuální počítač** otevře se dialogové okno.
   
    ![Dialogové okno Azure zachycení virtuálního počítače](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744143.png)
4. Zadejte popisek bitové kopie a popis. Popis a výchozí štítek jsou k dispozici, ale můžete jej přepsat vlastními Pokud chcete.
5. Pokud jste již spustili nástroj Sysprep na tomto virtuálním počítači, vyberte **na virtuální počítač mají spustit program Sysprep** pole.
   
    Nástroj Sysprep je nástroj, který mimo jiné systémy specifických dat odebere z virtuálního počítače verze systému Windows, což šablonu, kterou můžete použít jiné. V tématu [jak zachytit virtuální počítač Windows pro použití jako šablona](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) Další informace. Zálohování virtuálního počítače před spuštěním nástroje Sysprep.
6. Po dokončení konfigurace nastavení zachycení, vyberte **zaznamenat** tlačítko pro vytvoření kontrolního bodu.
   
    Jak Azure vytvoří kontrolní bod, **protokol činnosti Azure** zobrazuje průběh operace.
   
    ![Zaznamenání kontrolní bod virtuálního počítače](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744144.png)
   
    Po dokončení operace kontrolní bod, se zobrazí v **protokol činnosti Azure**.
   
    ![Kontrolní bod operace byla dokončena](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744145.png)

## <a name="to-manage-virtual-machine-checkpoints"></a>Chcete-li spravovat kontrolní body virtuálních počítačů
### <a name="to-restore-a-virtual-machine-to-a-previously-saved-state"></a>Virtuální počítač obnovit do předchozího uloženého stavu
* Postupujte podle kroků uvedených v [krok za krokem: provedení cloudu obnoví Microsoft virtuálních počítačů Azure pomocí prostředí PowerShell - část 2](http://blogs.technet.com/b/keithmayer/archive/2014/02/04/step-by-step-perform-cloud-restores-of-windows-azure-virtual-machines-using-powershell-part-2.aspx).

### <a name="to-delete-a-checkpoint"></a>Chcete-li odstranit kontrolní bod
1. Přejděte na [portál pro správu Azure](http://go.microsoft.com/fwlink/?LinkID=253103).
2. Na stránce konfigurace virtuálního počítače vyberte **bitové kopie** v horní části stránky.
3. Vyberte kontrolní bod, které chcete odstranit a potom vyberte **odstranit** tlačítko v dolní části stránky.

## <a name="shut-down-your-virtual-machine"></a>Vypnout virtuální počítač
1. V Průzkumníku serveru, vyberte virtuální počítač, které chcete vypnout v **virtuální počítače Azure** uzlu.
2. V místní nabídce vyberte buď **vypnutí** příkazu, nebo zvolte **konfigurace** zobrazit stránku konfigurace virtuálního počítače, a pak vyberte **vypnutí** tlačítko.

## <a name="next-steps"></a>Další kroky
Další informace o vytváření virtuálních počítačů najdete v tématu [vytvořit virtuálním počítači systémem Linux](../articles/virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a [vytvoření virtuálního počítače se systémem Windows na portálu Azure preview](../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

