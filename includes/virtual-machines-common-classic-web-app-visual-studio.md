

Když vytvoříte projekt webové aplikace pro Azure, můžete zřídit virtuální počítač v Azure. Můžete nakonfigurovat virtuální počítač pomocí další software nebo použití virtuálního počítače pro účely diagnostiky nebo ladění.

K vytvoření virtuálního počítače, když vytvoříte webovou aplikaci, postupujte podle těchto kroků:

1. V sadě Visual Studio, klikněte na tlačítko **souboru** > **nový** > **projektu** > **webové**a klikněte na tlačítko **Webová aplikace ASP.NET** (v části **Visual C#** nebo **jazyka Visual Basic** uzly).
2. V **nový projekt ASP.NET** dialogové okno, vyberte typ vytvářené webové aplikace a v Azure části dialogového okna (v pravém dolním rohu), ujistěte se, že **hostovat v cloudu** zaškrtávací políčko vybrané (Toto zaškrtávací políčko je označené jako **vytvořit vzdálené prostředky** některé instalace).
   
    ![][0]
3. Tento příklad, v rozevíracím seznamu v části Microsoft Azure, zvolte **virtuální počítač (v1)** a potom klikněte na tlačítko **OK** tlačítko.
4. Pokud budete vyzváni, přihlaste se k Azure. **Vytvořit virtuální počítač** zobrazí se dialogové okno.
   
    ![][2]
5. V **název DNS** pole, zadejte název pro virtuální počítač. Název DNS musí být jedinečný v Azure. Pokud zadaný název není k dispozici, zobrazí se červený vykřičník.
6. V **Image** , zvolte image chcete založit na virtuální počítač. Můžete vybrat kteroukoli z imagí standardní virtuálních počítačů Azure nebo bitové kopie, který jste nahráli do Azure.
7. Nechte **povolte službu IIS a nasazení webu** zaškrtávací políčko Vybrat, pokud chcete nainstalovat jiný webový server. Nebude možné publikovat ze sady Visual Studio, pokud zakážete, nasazení webu. Službu IIS a nasazení webu můžete přidat k libovolnému zabalené Image Windows serveru, včetně vlastních imagí.
8. V **velikost** , zvolte velikost virtuálního počítače.
9. Zadejte přihlašovací údaje pro tento virtuální počítač. Si poznamenejte, protože je budete potřebovat jejich přístupu k počítači přes vzdálenou plochu.
10. V **umístění** , zvolte oblast pro hostování tohoto virtuálního počítače.
11. Klikněte na tlačítko **OK** tlačítko zahájit vytváření virtuálního počítače. Můžete sledovat průběh operace **výstup** okna.
    
    ![][3]
12. Po zřízení virtuálního počítače, jsou publikované skripty vytvořené v **PublishScripts** uzlu ve vašem řešení. Publikované skript spustí a zřizuje virtuální počítač v Azure. **Výstup** okno zobrazuje stav. Skript provede následující akce pro nastavení virtuálního počítače:
    
    * Pokud ještě neexistuje, vytvoří virtuální počítač.
    * Vytvoří účet úložiště s názvem, který začíná `devtest`, ale pouze v případě, že v zadané oblasti není již účtu úložiště.
    * Vytvoří cloudovou službu jako kontejner pro virtuální počítač a vytvoří webové role pro webovou aplikaci.
    * Nakonfiguruje nasazení webu na virtuálním počítači.
    * Nakonfiguruje službu IIS a ASP.NET na virtuálním počítači.
    
    ![][4]
13. (Volitelné) Můžete připojit k novému virtuálnímu počítači. V **Průzkumníka serveru**, rozbalte **virtuálních počítačů** uzlu, vyberte uzel pro virtuální počítač, který jste vytvořili a na místní nabídku a zvolte **připojit pomocí vzdálené plochy**. Případně můžete **Průzkumníka cloudu** můžete zvolit **otevřít na portálu** v místní nabídce a připojte se k virtuálnímu počítači existuje.
    
    ![][5]

## <a name="next-steps"></a>Další postup
Pokud chcete přizpůsobit publikované skripty, které jste vytvořili, přečtěte si další podrobné informace na [pomocí skriptů Windows Powershellu k publikování do vývojových a testovacích prostředí](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png
