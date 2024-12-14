╰╴gdbus introspect --system --dest org.freedesktop.UPower --object-path /org/freedesktop/UPower
node /org/freedesktop/UPower {
  interface org.freedesktop.DBus.Properties {
    methods:
      Get(in  s interface_name,
          in  s property_name,
          out v value);
      GetAll(in  s interface_name,
             out a{sv} properties);
      Set(in  s interface_name,
          in  s property_name,
          in  v value);
    signals:
      PropertiesChanged(s interface_name,
                        a{sv} changed_properties,
                        as invalidated_properties);
    properties:
  };
  interface org.freedesktop.DBus.Introspectable {
    methods:
      Introspect(out s xml_data);
    signals:
    properties:
  };
  interface org.freedesktop.DBus.Peer {
    methods:
      Ping();
      GetMachineId(out s machine_uuid);
    signals:
    properties:
  };
  interface org.freedesktop.UPower {
    methods:
      @org.freedesktop.DBus.GLib.Async("")
      EnumerateDevices(out ao devices);
      @org.freedesktop.DBus.GLib.Async("")
      GetDisplayDevice(out o device);
      @org.freedesktop.DBus.GLib.Async("")
      GetCriticalAction(out s action);
    signals:
      DeviceAdded(o device);
      DeviceRemoved(o device);
    properties:
      readonly s DaemonVersion = '1.90.6';
      readonly b OnBattery = true;
      readonly b LidIsClosed = false;
      readonly b LidIsPresent = true;
  };
  node devices {
  };
  node KbdBacklight {
  };
};

[ initial state ]
╰╴gdbus call --system --dest net.hadess.PowerProfiles --object-path /net/hadess/PowerProfiles --method org.freedesktop.DBus.Properties.Get 'net.hadess.PowerProfiles' 'ActiveProfile'
(<'performance'>,)

╰╴gdbus call --system --dest org.freedesktop.UPower --object-path /org/freedesktop/UPower --method org.freedesktop.DBus.Properties.Get 'org.freedesktop.UPower' 'OnBattery'
(<true>,)

[ monitor AC use on going ]
╰╴gdbus monitor --system --dest org.freedesktop.UPower --object-path /org/freedesktop/UPower
Monitoring signals on object /org/freedesktop/UPower owned by org.freedesktop.UPower
The name org.freedesktop.UPower is owned by :1.12
/org/freedesktop/UPower: org.freedesktop.DBus.Properties.PropertiesChanged ('org.freedesktop.UPower', {'OnBattery': <false>}, @as [])
/org/freedesktop/UPower: org.freedesktop.DBus.Properties.PropertiesChanged ('org.freedesktop.UPower', {'OnBattery': <true>}, @as [])
/org/freedesktop/UPower: org.freedesktop.DBus.Properties.PropertiesChanged ('org.freedesktop.UPower', {'OnBattery': <false>}, @as [])
[ plugged in ]
/org/freedesktop/UPower: org.freedesktop.DBus.Properties.PropertiesChanged ('org.freedesktop.UPower', {'OnBattery': <true>}, @as [])
[ unplugged ]
/org/freedesktop/UPower: org.freedesktop.DBus.Properties.PropertiesChanged ('org.freedesktop.UPower', {'OnBattery': <false>}, @as [])
/org/freedesktop/UPower: org.freedesktop.DBus.Properties.PropertiesChanged ('org.freedesktop.UPower', {'OnBattery': <true>}, @as [])
/org/freedesktop/UPower: org.freedesktop.DBus.Properties.PropertiesChanged ('org.freedesktop.UPower', {'OnBattery': <false>}, @as [])
[ plugged in ]
/org/freedesktop/UPower: org.freedesktop.DBus.Properties.PropertiesChanged ('org.freedesktop.UPower', {'OnBattery': <true>}, @as [])
[ unplugged ]

[ changing and retrieving profile state]
╰╴gdbus call --system --dest net.hadess.PowerProfiles --object-path /net/hadess/PowerProfiles --method org.freedesktop.DBus.Properties.Set 'net.hadess.PowerProfiles' 'ActiveProfile' "<'balanced'>"
()

╰╴powerprofilesctl get
balanced

╰╴gdbus call --system --dest net.hadess.PowerProfiles --object-path /net/hadess/PowerProfiles --method org.freedesktop.DBus.Properties.Get 'net.hadess.PowerProfiles' 'ActiveProfile' 
(<'balanced'>,)
 
 ╰╴gdbus call --system --dest net.hadess.PowerProfiles --object-path /net/hadess/PowerProfiles --method org.freedesktop.DBus.Properties.Set 'net.hadess.PowerProfiles' 'ActiveProfile' "<'balanced'>"
()
╭╢ ⌁56%  X  mmicene  ~  Projects  github  auto_power_profile  main(+5/-1)  
╰╴gdbus call --system --dest net.hadess.PowerProfiles --object-path /net/hadess/PowerProfiles --method org.freedesktop.DBus.Properties.Set 'net.hadess.PowerProfiles' 'ActiveProfile' "<'performance'>"
()

╰╴gdbus call --system --dest net.hadess.PowerProfiles --object-path /net/hadess/PowerProfiles --method org.freedesktop.DBus.Properties.Get 'net.hadess.PowerProfiles' 'ActiveProfile' 
(<'performance'>,)

╰╴powerprofilesctl get
performance

╰╴gdbus call --system --dest net.hadess.PowerProfiles --object-path /net/hadess/PowerProfiles --method org.freedesktop.DBus.Properties.Set 'net.hadess.PowerProfiles' 'ActiveProfile' "<'balanced'>"
()

╰╴powerprofilesctl get
balanced

╰╴gdbus call --system --dest net.hadess.PowerProfiles --object-path /net/hadess/PowerProfiles --method org.freedesktop.DBus.Properties.Set 'net.hadess.PowerProfiles' 'ActiveProfile' "<'balanced'>"
()

╰╴powerprofilesctl get
balanced

[ what happens in monitoring when gnome fires off low power @ 20%]
╰╴gdbus monitor --system --dest org.freedesktop.UPower --object-path /org/freedesktop/UPower
Monitoring signals on object /org/freedesktop/UPower owned by org.freedesktop.UPower
The name org.freedesktop.UPower is owned by :1.12
^C

╰╴gdbus call --system --dest net.hadess.PowerProfiles --object-path /net/hadess/PowerProfiles --method org.freedesktop.DBus.Properties.Get 'net.hadess.PowerProfiles' 'ActiveProfile' 
(<'power-saver'>,)

╰╴powerprofilesctl get
power-saver

[ what happens when plugged in after gnome sets low power]
gdbus monitor --system --dest org.freedesktop.UPower --object-path /org/freedesktop/UPower
Monitoring signals on object /org/freedesktop/UPower owned by org.freedesktop.UPower
The name org.freedesktop.UPower is owned by :1.12
/org/freedesktop/UPower: org.freedesktop.DBus.Properties.PropertiesChanged ('org.freedesktop.UPower', {'OnBattery': <false>}, @as [])


╰╴gdbus call --system --dest net.hadess.PowerProfiles --object-path /net/hadess/PowerProfiles --method org.freedesktop.DBus.Properties.Get 'net.hadess.PowerProfiles' 'ActiveProfile' 
(<'balanced'>,)
 
╰╴powerprofilesctl get
balanced
