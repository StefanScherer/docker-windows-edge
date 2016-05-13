# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

$script = <<SCRIPT
$link = "https://s3-us-west-1.amazonaws.com/app-beta-download/Docker.msi"
$version = "2.0.1832"
$msi = "C:\\Vagrant\\Docker-$version.msi"
$desktop = "$env:USERPROFILE\\Desktop\\Docker-$version.msi"
if (!(Test-Path $msi)) {
  Write-Host "Downloading MSI to shared folder."
  wget -out $msi $link
}
Write-Host "Copying MSI to desktop."
copy $msi $desktop
Write-Host "Now double-click the MSI file."
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All -NoRestart
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "windows_10"
  config.vm.network :forwarded_port, guest: 5985, host: 5985, id: "winrm", auto_correct: true

  config.vm.communicator = "winrm"

  config.winrm.username = "vagrant"
  config.winrm.password = "vagrant"

  config.vm.guest = :windows
  config.windows.halt_timeout = 15

  config.vm.provision "shell", inline: $script, privileged: false
  config.vm.provision "reload"

  ["vmware_fusion", "vmware_workstation"].each do |provider|
    config.vm.provider provider do |v, override|
      v.gui = true
      v.vmx["memsize"] = "4096"
      v.vmx["numvcpus"] = "2"
      v.vmx["vhv.enable"] = "TRUE"
    end
  end

  config.vm.provider "vmware_fusion" do |v|
    v.vmx["gui.fitguestusingnativedisplayresolution"] = "TRUE"
    v.vmx["mks.enable3d"] = "TRUE"
    v.vmx["mks.forceDiscreteGPU"] = "TRUE"
    v.vmx["gui.fullscreenatpoweron"] = "TRUE"
    v.vmx["gui.viewmodeatpoweron"] = "fullscreen"
    v.vmx["gui.lastPoweredViewMode"] = "fullscreen"
    v.enable_vmrun_ip_lookup = false
  end
end
