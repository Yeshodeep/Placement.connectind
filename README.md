import { useState, useEffect } from 'react';
import { StudentDashboard } from './components/StudentDashboard';
import { RecruiterDashboard } from './components/RecruiterDashboard';
import { HODDashboard } from './components/HODDashboard';
import { LoginForm } from './components/LoginForm';
import { SignUpForm } from './components/SignUpForm';
import { ForgotPasswordForm } from './components/ForgotPasswordForm';
import { Tabs, TabsContent, TabsList, TabsTrigger } from './components/ui/tabs';
import { Card } from './components/ui/card';
import { Button } from './components/ui/button';
import { GraduationCap, Building2, UserCheck, MapPin, LogOut } from 'lucide-react';
import { ImageWithFallback } from './components/figma/ImageWithFallback';
import { Toaster } from './components/ui/sonner';
import { AuthUser, getCurrentUser, logout as authLogout, initializeAuth } from './utils/auth';

type UserRole = 'student' | 'recruiter' | 'hod' | null;
type AuthView = 'role-selection' | 'login' | 'signup' | 'forgot-password';

export default function App() {
  const [activeTab, setActiveTab] = useState('student');
  const [userRole, setUserRole] = useState<UserRole>(null);
  const [authView, setAuthView] = useState<AuthView>('role-selection');
  const [selectedRole, setSelectedRole] = useState<UserRole>(null);
  const [currentUser, setCurrentUser] = useState<AuthUser | null>(null);

  // Initialize auth and check for existing session
  useEffect(() => {
    initializeAuth();
    const user = getCurrentUser();
    if (user) {
      setCurrentUser(user);
      setUserRole(user.role);
      setActiveTab(user.role);
    }
  }, []);

  // Handle logout
  const handleLogout = () => {
    authLogout();
    setUserRole(null);
    setCurrentUser(null);
    setActiveTab('student');
    setAuthView('role-selection');
    setSelectedRole(null);
  };

  // Handle role selection
  const handleRoleSelect = (role: UserRole) => {
    setSelectedRole(role);
    setAuthView('login');
  };

  // Handle login success
  const handleLoginSuccess = (user: AuthUser) => {
    setCurrentUser(user);
    setUserRole(user.role);
    setActiveTab(user.role);
  };

  // Handle sign-up success
  const handleSignUpSuccess = () => {
    setAuthView('login');
  };

  // Login screen
  if (!userRole) {
    return (
      <div className="min-h-screen bg-gradient-to-br from-blue-50 via-white to-purple-50">
        <Toaster />
        
        {/* Header */}
        <header className="bg-white border-b shadow-sm">
          <div className="container mx-auto px-4 py-4">
            <div className="flex items-center gap-3">
              <div className="bg-gradient-to-br from-blue-600 to-purple-600 p-2 rounded-lg">
                <MapPin className="h-6 w-6 text-white" />
              </div>
              <div>
                <h1 className="text-xl">PlacementConnect India</h1>
                <p className="text-xs text-muted-foreground">Connecting Students, Recruiters & Colleges</p>
              </div>
            </div>
          </div>
        </header>

        {/* Auth Section */}
        <div className="container mx-auto px-4 py-16">
          <div className="max-w-4xl mx-auto">
            {authView === 'role-selection' && (
              <>
                <div className="text-center mb-12">
                  <h2 className="text-4xl mb-4">Welcome to PlacementConnect</h2>
                  <p className="text-muted-foreground text-lg">
                    Choose your role to continue
                  </p>
                </div>

                <div className="grid md:grid-cols-3 gap-6">
                  {/* Student Login */}
                  <Card className="p-6 hover:shadow-lg transition-shadow">
                    <div className="text-center">
                      <div className="bg-blue-100 w-16 h-16 rounded-full flex items-center justify-center mx-auto mb-4">
                        <GraduationCap className="h-8 w-8 text-blue-600" />
                      </div>
                      <h3 className="text-xl mb-2">Student</h3>
                      <p className="text-sm text-muted-foreground mb-6">
                        Browse placement drives and apply to opportunities
                      </p>
                      <Button 
                        onClick={() => handleRoleSelect('student')}
                        className="w-full"
                      >
                        Continue as Student
                      </Button>
                    </div>
                  </Card>

                  {/* Recruiter Login */}
                  <Card className="p-6 hover:shadow-lg transition-shadow">
                    <div className="text-center">
                      <div className="bg-purple-100 w-16 h-16 rounded-full flex items-center justify-center mx-auto mb-4">
                        <Building2 className="h-8 w-8 text-purple-600" />
                      </div>
                      <h3 className="text-xl mb-2">Recruiter</h3>
                      <p className="text-sm text-muted-foreground mb-6">
                        Post placement drives and manage applications
                      </p>
                      <Button 
                        onClick={() => handleRoleSelect('recruiter')}
                        className="w-full"
                      >
                        Continue as Recruiter
                      </Button>
                    </div>
                  </Card>

                  {/* HOD Login */}
                  <Card className="p-6 hover:shadow-lg transition-shadow">
                    <div className="text-center">
                      <div className="bg-green-100 w-16 h-16 rounded-full flex items-center justify-center mx-auto mb-4">
                        <UserCheck className="h-8 w-8 text-green-600" />
                      </div>
                      <h3 className="text-xl mb-2">College HOD</h3>
                      <p className="text-sm text-muted-foreground mb-6">
                        Approve drives and monitor student placements
                      </p>
                      <Button 
                        onClick={() => handleRoleSelect('hod')}
                        className="w-full"
                      >
                        Continue as HOD
                      </Button>
                    </div>
                  </Card>
                </div>
              </>
            )}

            {authView === 'login' && selectedRole && (
              <LoginForm
                role={selectedRole}
                onSuccess={handleLoginSuccess}
                onSignUpClick={() => setAuthView('signup')}
                onForgotPasswordClick={() => setAuthView('forgot-password')}
              />
            )}

            {authView === 'signup' && selectedRole && (
              <SignUpForm
                role={selectedRole}
                onSuccess={handleSignUpSuccess}
                onBackToLogin={() => setAuthView('login')}
              />
            )}

            {authView === 'forgot-password' && (
              <ForgotPasswordForm
                onBackToLogin={() => setAuthView('login')}
              />
            )}
          </div>
        </div>

        {/* Footer */}
        <footer className="bg-gray-900 text-white mt-12">
          <div className="container mx-auto px-4 py-8">
            <div className="grid md:grid-cols-4 gap-8">
              <div>
                <div className="flex items-center gap-2 mb-4">
                  <div className="bg-gradient-to-br from-blue-600 to-purple-600 p-2 rounded-lg">
                    <MapPin className="h-5 w-5 text-white" />
                  </div>
                  <span>PlacementConnect</span>
                </div>
                <p className="text-sm text-gray-400">
                  Bridging the gap between talent and opportunity across India.
                </p>
              </div>
              <div>
                <h3 className="mb-3">For Students</h3>
                <ul className="space-y-2 text-sm text-gray-400">
                  <li>Browse Drives</li>
                  <li>Register Online</li>
                  <li>Track Applications</li>
                  <li>Career Resources</li>
                </ul>
              </div>
              <div>
                <h3 className="mb-3">For Recruiters</h3>
                <ul className="space-y-2 text-sm text-gray-400">
                  <li>Post Drives</li>
                  <li>Manage Applications</li>
                  <li>College Network</li>
                  <li>Analytics</li>
                </ul>
              </div>
              <div>
                <h3 className="mb-3">For Colleges</h3>
                <ul className="space-y-2 text-sm text-gray-400">
                  <li>Approve Drives</li>
                  <li>Monitor Placements</li>
                  <li>Student Management</li>
                  <li>Reports</li>
                </ul>
              </div>
            </div>
            <div className="border-t border-gray-800 mt-8 pt-6 text-center text-sm text-gray-400">
              <p>&copy; 2025 PlacementConnect India. All rights reserved.</p>
            </div>
          </div>
        </footer>
      </div>
    );
  }

  // Determine which tabs to show based on user role
  const showStudentTab = userRole === 'student';
  const showRecruiterTab = userRole === 'recruiter';
  const showHODTab = true; // HOD tab visible to all logged-in users

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 via-white to-purple-50">
      <Toaster />
      
      {/* Header */}
      <header className="bg-white border-b shadow-sm sticky top-0 z-50">
        <div className="container mx-auto px-4 py-4">
          <div className="flex items-center justify-between">
            <div className="flex items-center gap-3">
              <div className="bg-gradient-to-br from-blue-600 to-purple-600 p-2 rounded-lg">
                <MapPin className="h-6 w-6 text-white" />
              </div>
              <div>
                <h1 className="text-xl">PlacementConnect India</h1>
                <p className="text-xs text-muted-foreground">Connecting Students, Recruiters & Colleges</p>
              </div>
            </div>
            <div className="flex items-center gap-4">
              <div className="text-right">
                <p className="text-sm text-muted-foreground">Logged in as</p>
                <p className="text-sm capitalize">{userRole}</p>
              </div>
              <Button 
                variant="outline" 
                size="sm" 
                onClick={handleLogout}
                className="gap-2"
              >
                <LogOut className="h-4 w-4" />
                <span className="hidden sm:inline">Logout</span>
              </Button>
            </div>
          </div>
        </div>
      </header>

      {/* Hero Section */}
      <div className="bg-gradient-to-r from-blue-600 to-purple-600 text-white">
        <div className="container mx-auto px-4 py-8">
          <div className="grid md:grid-cols-2 gap-8 items-center">
            <div>
              <h2 className="text-3xl mb-3">Find Your Dream Job Across India</h2>
              <p className="text-blue-100 mb-6">
                Discover placement drives from top companies, connect with recruiters, 
                and launch your career. Real-time updates from colleges nationwide.
              </p>
              <div className="flex flex-wrap gap-4 text-sm">
                <div className="flex items-center gap-2 bg-white/10 backdrop-blur-sm px-4 py-2 rounded-full">
                  <Building2 className="h-4 w-4" />
                  <span>500+ Companies</span>
                </div>
                <div className="flex items-center gap-2 bg-white/10 backdrop-blur-sm px-4 py-2 rounded-full">
                  <GraduationCap className="h-4 w-4" />
                  <span>1000+ Colleges</span>
                </div>
                <div className="flex items-center gap-2 bg-white/10 backdrop-blur-sm px-4 py-2 rounded-full">
                  <MapPin className="h-4 w-4" />
                  <span>All Major Cities</span>
                </div>
              </div>
            </div>
            <div className="hidden md:block">
              <ImageWithFallback
                src="https://images.unsplash.com/photo-1635246550194-11af93a2763f?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w3Nzg4Nzd8MHwxfHNlYXJjaHwxfHxpbmRpYSUyMGNvbGxlZ2UlMjBzdHVkZW50c3xlbnwxfHx8fDE3NjI1MDQyMzl8MA&ixlib=rb-4.1.0&q=80&w=1080&utm_source=figma&utm_medium=referral"
                alt="Students"
                className="rounded-lg shadow-2xl w-full h-64 object-cover"
              />
            </div>
          </div>
        </div>
      </div>

      {/* Main Content */}
      <div className="container mx-auto px-4 py-8">
        <Card className="p-6">
          <Tabs value={activeTab} onValueChange={setActiveTab}>
            <TabsList className={`grid w-full ${userRole === 'hod' ? 'grid-cols-1' : 'grid-cols-2'} mb-6`}>
              {showStudentTab && (
                <TabsTrigger value="student" className="gap-2">
                  <GraduationCap className="h-4 w-4" />
                  <span className="hidden sm:inline">Student Dashboard</span>
                  <span className="sm:hidden">Student</span>
                </TabsTrigger>
              )}
              {showRecruiterTab && (
                <TabsTrigger value="recruiter" className="gap-2">
                  <Building2 className="h-4 w-4" />
                  <span className="hidden sm:inline">Recruiter Dashboard</span>
                  <span className="sm:hidden">Recruiter</span>
                </TabsTrigger>
              )}
              {showHODTab && (
                <TabsTrigger value="hod" className="gap-2">
                  <UserCheck className="h-4 w-4" />
                  <span className="hidden sm:inline">College HOD Dashboard</span>
                  <span className="sm:hidden">HOD</span>
                </TabsTrigger>
              )}
            </TabsList>

            {showStudentTab && (
              <TabsContent value="student">
                <StudentDashboard />
              </TabsContent>
            )}

            {showRecruiterTab && (
              <TabsContent value="recruiter">
                <RecruiterDashboard />
              </TabsContent>
            )}

            {showHODTab && (
              <TabsContent value="hod">
                <HODDashboard />
              </TabsContent>
            )}
          </Tabs>
        </Card>
      </div>

      {/* Footer */}
      <footer className="bg-gray-900 text-white mt-12">
        <div className="container mx-auto px-4 py-8">
          <div className="grid md:grid-cols-4 gap-8">
            <div>
              <div className="flex items-center gap-2 mb-4">
                <div className="bg-gradient-to-br from-blue-600 to-purple-600 p-2 rounded-lg">
                  <MapPin className="h-5 w-5 text-white" />
                </div>
                <span>PlacementConnect</span>
              </div>
              <p className="text-sm text-gray-400">
                Bridging the gap between talent and opportunity across India.
              </p>
            </div>
            <div>
              <h3 className="mb-3">For Students</h3>
              <ul className="space-y-2 text-sm text-gray-400">
                <li>Browse Drives</li>
                <li>Register Online</li>
                <li>Track Applications</li>
                <li>Career Resources</li>
              </ul>
            </div>
            <div>
              <h3 className="mb-3">For Recruiters</h3>
              <ul className="space-y-2 text-sm text-gray-400">
                <li>Post Drives</li>
                <li>Manage Applications</li>
                <li>College Network</li>
                <li>Analytics</li>
              </ul>
            </div>
            <div>
              <h3 className="mb-3">For Colleges</h3>
              <ul className="space-y-2 text-sm text-gray-400">
                <li>Approve Drives</li>
                <li>Monitor Placements</li>
                <li>Student Management</li>
                <li>Reports</li>
              </ul>
            </div>
          </div>
          <div className="border-t border-gray-800 mt-8 pt-6 text-center text-sm text-gray-400">
            <p>&copy; 2025 PlacementConnect India. All rights reserved.</p>
          </div>
        </div>
      </footer>
    </div>
  );
}
