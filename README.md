# SwiftUIOSC


## Install

In Xcode go to *File* /  *Swift Packages* / *Add Package Dependecy...* and enter the repo url:
```
https://github.com/heestand-xyz/SwiftUIOSC
```

Import the package:

```swift
import SwiftUI
import SwiftUIOSC
```

Create a `OSCState` with a osc address.

## TL;DR

```swift
struct ContentView: View {
    @OSCState(name: "test") var test: CGFloat = 0.0
    var body: some View {
        Slider(value: $test)
        .onAppear {
            OSC.shared.clientAddress = "localhost"
            OSC.shared.clientPort = 8000
        }
    }
}
```

## Demo Example

Demo project avalible in repo.

```swift
struct ContentView: View {
    
    @ObservedObject var osc: OSC = .shared
    
    @OSCState(name: "test/float") var testFloat: CGFloat = 0.0
    @OSCState(name: "test/int") var testInt: Int = 0
    @OSCState(name: "test/string") var testString: String = ""
    
    var body: some View {
        
        VStack {
            
            // Connection
            HStack {
                if osc.connection.isConnected {
                    Text("Connected on")
                } else {
                    Text("Connection is")
                }
                switch osc.connection {
                case .unknown:
                    Label("Unknown", systemImage: "wifi.exclamationmark")
                case .offline:
                    Label("Offline", systemImage: "wifi.slash")
                case .wifi:
                    Label("Wi-Fi", systemImage: "wifi")
                case .cellular:
                    Label("Cellular", systemImage: "antenna.radiowaves.left.and.right")
                }
            }
            
            Divider()
            
            // Test Float
            HStack {
                
                Text("Float")
                    .fontWeight(.bold)
                    .frame(width: 75, alignment: .trailing)
                
                Button {
                    testFloat = 0.0
                } label: {
                    Text("Zero")
                }
                .disabled(testFloat == 0.0)
                
                Slider(value: $testFloat)
                
                Text("\(testFloat, specifier: "%.2f")")
                
            }
            
            // Test Int
            HStack {
            
                Text("Int")
                    .fontWeight(.bold)
                    .frame(width: 75, alignment: .trailing)
                
                Picker("", selection: $testInt) {
                    Text("First").tag(0)
                    Text("Second").tag(1)
                    Text("Third").tag(2)
                }
                .pickerStyle(SegmentedPickerStyle())
    
            }
            
            // Test String
            HStack {
            
                Text("String")
                    .fontWeight(.bold)
                    .frame(width: 75, alignment: .trailing)
                
                TextField("Text", text: $testString)
                
            }
            
            Divider()
            
            // Info
            VStack(alignment: .leading) {
                
                // Server
                HStack {
                    HStack {
                        Text("Server")
                            .fontWeight(.bold)
                        Text("IP Address:")
                    }
                    .frame(width: 200, alignment: .trailing)
                    Text(osc.serverAddress ?? "unknown")
                }
                HStack {
                    Text("Port:")
                        .frame(width: 200, alignment: .trailing)
                    TextField("Port", text: Binding<String>(get: {
                        "\(osc.serverPort)"
                    }, set: { text in
                        guard let port = Int(text) else { return }
                        osc.serverPort = port
                    }))
                }
                
                // Client
                HStack {
                    HStack {
                        Text("Client")
                            .fontWeight(.bold)
                        Text("IP Address:")
                    }
                    .frame(width: 200, alignment: .trailing)
                    TextField("Address", text: $osc.clientAddress)
                }
                HStack {
                    Text("Port:")
                        .frame(width: 200, alignment: .trailing)
                    TextField("Port", text: Binding<String>(get: {
                        "\(osc.clientPort)"
                    }, set: { text in
                        guard let port = Int(text) else { return }
                        osc.clientPort = port
                    }))
                }
                
            }
            
        }
        .font(.system(.body, design: .monospaced))
        .frame(minWidth: 300, maxWidth: 400)
        .padding()
        .onAppear {
            osc.serverPort = 7000
            osc.clientAddress = "localhost"
            osc.clientPort = 8000
        }
        
    }
    
}
```

## macOS Networking

> If you got a **macOS** target in your project make sure to enable **Networking** under "Signing & Capabilities" / "App Sandbox":
> Enable "Incoming Connections (Server)" and "Outgoing Connections (Client)"
