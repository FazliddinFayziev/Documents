```
  import React, { useContext, ReactNode } from 'react';

interface AppContextProps {
    home: string;
}

const AppContext = React.createContext<AppContextProps | undefined>(undefined);

interface AppProviderProps {
    children: ReactNode;
}

export const AppProvider: React.FC<AppProviderProps> = ({ children }) => {
    const home = "home";

    return (
        <AppContext.Provider value={{ home }}>
            {children}
        </AppContext.Provider>
    );
}

export const useGlobalContext = (): AppContextProps => {
    const context = useContext(AppContext);
    if (!context) {
        throw new Error('useGlobalContext must be used within an AppProvider');
    }
    return context;
}
```
