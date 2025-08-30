classDiagram
  class EmployeeController
  class LeaveController
  class BalanceController

  class EmployeeService {
    +createEmployee(EmployeeCreateDto): EmployeeDto
    +getEmployee(UUID): EmployeeDto
    +listEmployees(page,size): Page~EmployeeDto~
    +updateStatus(UUID, status): void
  }

  class LeaveService {
    +applyLeave(LeaveApplyDto): LeaveDto
    +approve(UUID, ApproveDto): LeaveDto
    +reject(UUID, RejectDto): LeaveDto
    +cancel(UUID): LeaveDto
    +listLeaves(filter): Page~LeaveDto~
  }

  class BalanceService {
    +allocateYear(UUID, year, allocation): BalanceDto
    +getEmployeeBalance(UUID, year): BalanceDto
    +recompute(UUID, year): BalanceDto
  }

  class EmployeeRepository
  class LeaveRepository
  class BalanceRepository
  class TransactionRepository

  class Employee
  class LeaveRequest
  class LeaveBalance
  class LeaveTransaction

  EmployeeController --> EmployeeService
  LeaveController --> LeaveService
  BalanceController --> BalanceService

  EmployeeService --> EmployeeRepository
  LeaveService --> LeaveRepository
  LeaveService --> BalanceService
  BalanceService --> BalanceRepository
  BalanceService --> TransactionRepository

  EmployeeRepository --> Employee
  LeaveRepository --> LeaveRequest
  BalanceRepository --> LeaveBalance
  TransactionRepository --> LeaveTransaction
