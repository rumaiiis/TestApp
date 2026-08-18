foreach ($app in "Word","Excel") { try { $o = New-Object -ComObject "$app.Application"; "$app OK - version $($o.Version)"; $o.Quit() } catch { "$app NOT installed" } }
