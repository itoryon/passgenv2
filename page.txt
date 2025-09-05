"use client"

import { useState, useEffect } from "react"
import { Button } from "@/components/ui/button"
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card"
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"
import { Slider } from "@/components/ui/slider"
import { Checkbox } from "@/components/ui/checkbox"
import { Badge } from "@/components/ui/badge"
import { Copy, RefreshCw, Shield, Check } from "lucide-react"

export default function PasswordGenerator() {
  const [password, setPassword] = useState("")
  const [length, setLength] = useState([12])
  const [includeUppercase, setIncludeUppercase] = useState(true)
  const [includeLowercase, setIncludeLowercase] = useState(true)
  const [includeNumbers, setIncludeNumbers] = useState(true)
  const [includeSymbols, setIncludeSymbols] = useState(false)
  const [strength, setStrength] = useState("")
  const [copied, setCopied] = useState(false)

  const generatePassword = () => {
    if (!includeUppercase && !includeLowercase && !includeNumbers && !includeSymbols) {
      setPassword("Выберите хотя бы один вариант")
      setStrength("")
      return
    }

    let charset = ""
    if (includeUppercase) charset += "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    if (includeLowercase) charset += "abcdefghijklmnopqrstuvwxyz"
    if (includeNumbers) charset += "0123456789"
    if (includeSymbols) charset += "!@#$%^&*()_+-=[]{}|;:,.<>?"

    let newPassword = ""
    for (let i = 0; i < length[0]; i++) {
      const randomIndex = Math.floor(Math.random() * charset.length)
      newPassword += charset[randomIndex]
    }

    setPassword(newPassword)
    calculateStrength(newPassword)
  }

  const calculateStrength = (pwd: string) => {
    let score = 0

    if (pwd.length > 11) score += 2
    else if (pwd.length > 7) score += 1

    if (/[A-Z]/.test(pwd)) score += 1
    if (/[a-z]/.test(pwd)) score += 1
    if (/[0-9]/.test(pwd)) score += 1
    if (/[^A-Za-z0-9]/.test(pwd)) score += 2

    if (score < 3) setStrength("weak")
    else if (score < 6) setStrength("medium")
    else setStrength("strong")
  }

  const copyToClipboard = async () => {
    if (password && password !== "Выберите хотя бы один вариант") {
      try {
        await navigator.clipboard.writeText(password)
        setCopied(true)
        setTimeout(() => setCopied(false), 2000)
      } catch (err) {
        console.error("Failed to copy password:", err)
      }
    }
  }

  const resetSettings = () => {
    setLength([12])
    setIncludeUppercase(true)
    setIncludeLowercase(true)
    setIncludeNumbers(true)
    setIncludeSymbols(false)
    generatePassword()
  }

  useEffect(() => {
    generatePassword()
  }, [length, includeUppercase, includeLowercase, includeNumbers, includeSymbols])

  const getStrengthColor = () => {
    switch (strength) {
      case "weak":
        return "bg-destructive text-destructive-foreground"
      case "medium":
        return "bg-yellow-500 text-white"
      case "strong":
        return "bg-green-500 text-white"
      default:
        return "bg-muted text-muted-foreground"
    }
  }

  const getStrengthText = () => {
    switch (strength) {
      case "weak":
        return "Слабая"
      case "medium":
        return "Средняя"
      case "strong":
        return "Высокая"
      default:
        return "Не определена"
    }
  }

  return (
    <div className="min-h-screen bg-gradient-to-br from-purple-600 via-pink-500 to-orange-400 animate-gradient-x flex items-center justify-center p-4 relative overflow-hidden">
      <div className="absolute inset-0 bg-gradient-to-r from-blue-600/20 via-purple-600/20 to-pink-600/20 animate-pulse"></div>
      <div className="absolute top-10 left-10 w-32 h-32 bg-gradient-to-r from-cyan-400 to-blue-500 rounded-full blur-xl opacity-70 animate-bounce"></div>
      <div className="absolute bottom-10 right-10 w-40 h-40 bg-gradient-to-r from-pink-400 to-red-500 rounded-full blur-xl opacity-60 animate-pulse"></div>
      <div className="absolute top-1/2 left-1/4 w-24 h-24 bg-gradient-to-r from-green-400 to-emerald-500 rounded-full blur-lg opacity-50 animate-bounce delay-1000"></div>

      <div className="w-full max-w-md space-y-6 relative z-10">
        {/* Header */}
        <div className="text-center space-y-2">
          <div className="flex items-center justify-center gap-3 mb-4">
            <div className="p-3 bg-gradient-to-r from-cyan-400 to-blue-500 rounded-full shadow-lg shadow-blue-500/50">
              <Shield className="h-6 w-6 text-white" />
            </div>
            <h1 className="text-3xl font-bold bg-gradient-to-r from-white via-yellow-200 to-pink-200 bg-clip-text text-transparent">
              Генератор паролей
            </h1>
          </div>
          <p className="text-white/90 text-balance font-medium">
            Создавайте надежные пароли для защиты ваших аккаунтов
          </p>
        </div>

        {/* Password Display */}
        <Card className="bg-white/95 backdrop-blur-sm border-0 shadow-2xl shadow-purple-500/25 ring-1 ring-white/20">
          <CardContent className="p-6">
            <div className="space-y-4">
              <div className="relative">
                <div className="relative">
                  <div className="absolute inset-0 bg-gradient-to-r from-purple-500 to-pink-500 rounded-lg blur opacity-75"></div>
                  <Input
                    value={password}
                    readOnly
                    className="relative bg-white pr-12 font-mono text-center text-lg border-2 border-transparent focus:border-gradient-to-r focus:from-purple-500 focus:to-pink-500"
                    placeholder="Сгенерированный пароль появится здесь"
                  />
                </div>
                <Button
                  size="sm"
                  variant="ghost"
                  className="absolute right-1 top-1/2 -translate-y-1/2 h-8 w-8 p-0 hover:bg-gradient-to-r hover:from-purple-500 hover:to-pink-500 hover:text-white transition-all duration-300"
                  onClick={copyToClipboard}
                  disabled={!password || password === "Выберите хотя бы один вариант"}
                >
                  {copied ? <Check className="h-4 w-4 text-green-500" /> : <Copy className="h-4 w-4" />}
                </Button>
              </div>

              {strength && (
                <div className="flex items-center justify-between">
                  <span className="text-sm text-gray-600 font-medium">Надежность:</span>
                  <Badge className={`${getStrengthColor()} shadow-lg`}>{getStrengthText()}</Badge>
                </div>
              )}
            </div>
          </CardContent>
        </Card>

        {/* Settings */}
        <Card className="bg-white/95 backdrop-blur-sm border-0 shadow-2xl shadow-pink-500/25 ring-1 ring-white/20">
          <CardHeader>
            <CardTitle className="text-lg bg-gradient-to-r from-purple-600 to-pink-600 bg-clip-text text-transparent">
              Настройки пароля
            </CardTitle>
          </CardHeader>
          <CardContent className="space-y-6">
            {/* Length Slider */}
            <div className="space-y-3">
              <div className="flex items-center justify-between">
                <Label htmlFor="length" className="font-medium text-gray-700">
                  Длина пароля
                </Label>
                <Badge variant="secondary" className="bg-gradient-to-r from-blue-50 to-purple-50 text-white shadow-md">
                  {length[0]} символов
                </Badge>
              </div>
              <Slider
                id="length"
                min={4}
                max={100}
                step={1}
                value={length}
                onValueChange={setLength}
                className="w-full"
              />
            </div>

            {/* Character Types */}
            <div className="space-y-4">
              <Label className="font-medium text-gray-700">Включить символы:</Label>
              <div className="grid grid-cols-2 gap-4">
                <div className="flex items-center space-x-2 p-2 rounded-lg bg-gradient-to-r from-red-50 to-pink-50 border border-red-200">
                  <Checkbox id="uppercase" checked={includeUppercase} onCheckedChange={setIncludeUppercase} />
                  <Label htmlFor="uppercase" className="text-sm font-medium text-red-700">
                    A-Z
                  </Label>
                </div>
                <div className="flex items-center space-x-2 p-2 rounded-lg bg-gradient-to-r from-blue-50 to-cyan-50 border border-blue-200">
                  <Checkbox id="lowercase" checked={includeLowercase} onCheckedChange={setIncludeLowercase} />
                  <Label htmlFor="lowercase" className="text-sm font-medium text-blue-700">
                    a-z
                  </Label>
                </div>
                <div className="flex items-center space-x-2 p-2 rounded-lg bg-gradient-to-r from-green-50 to-emerald-50 border border-green-200">
                  <Checkbox id="numbers" checked={includeNumbers} onCheckedChange={setIncludeNumbers} />
                  <Label htmlFor="numbers" className="text-sm font-medium text-green-700">
                    0-9
                  </Label>
                </div>
                <div className="flex items-center space-x-2 p-2 rounded-lg bg-gradient-to-r from-purple-50 to-pink-50 border border-purple-200">
                  <Checkbox id="symbols" checked={includeSymbols} onCheckedChange={setIncludeSymbols} />
                  <Label htmlFor="symbols" className="text-sm font-medium text-purple-700">
                    !@#$
                  </Label>
                </div>
              </div>
            </div>
          </CardContent>
        </Card>

        {/* Action Buttons */}
        <div className="flex gap-3">
          <Button
            onClick={generatePassword}
            className="flex-1 bg-gradient-to-r from-purple-600 to-pink-600 hover:from-purple-700 hover:to-pink-700 text-white shadow-lg shadow-purple-500/50 transition-all duration-300 transform hover:scale-105"
          >
            <Shield className="h-4 w-4 mr-2" />
            Сгенерировать
          </Button>
          <Button
            variant="outline"
            onClick={resetSettings}
            className="bg-white/90 border-2 border-gradient-to-r from-orange-400 to-red-500 hover:bg-gradient-to-r hover:from-orange-400 hover:to-red-500 hover:text-white transition-all duration-300"
          >
            <RefreshCw className="h-4 w-4" />
          </Button>
        </div>

        {/* Copy Success Message */}
        {copied && (
          <div className="fixed top-4 left-1/2 -translate-x-1/2 z-50">
            <Card className="bg-gradient-to-r from-green-400 to-emerald-500 text-white border-0 shadow-2xl shadow-green-500/50">
              <CardContent className="p-3">
                <div className="flex items-center gap-2">
                  <Check className="h-4 w-4" />
                  <span className="text-sm font-medium">Пароль скопирован!</span>
                </div>
              </CardContent>
            </Card>
          </div>
        )}
      </div>
    </div>
  )
}
